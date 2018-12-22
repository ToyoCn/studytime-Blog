---
layout: post
title: Laravel异常捕获的处理
category: Laravel 
tags: [Laravel]
---

异常是运行中超出了你程序预期的一个东西。

## 目录

### 什么是异常
> 异常是运行中超出了你程序预期的一个东西。

### 如何使用异常
在 Laravel 中已经定义了很多异常，例如  ModelNotFoundException   AuthorizationException   MassAssignmentException   HttpResponseException  等等，基本每个模块都会定义一些异常。

### 场景 
例如京东有个  轻松购  的功能，当点击的时候会将该商品自动添加到购物车并生成订单，然后进行支付，这是一个网络请求，但是在后端实际执行了一系列的事情（以下操作是简单举例子便于说明问题，和真实步骤有差异）
1. 验证用户是否登录
2. 验证用户状态（如果被拉入系统黑名单就不能登录）
3. 查看订单中物品是否实时有货
4. 锁定货物（库存减少，支付中的货物数量+1）
5. 生成订单

### 问题
步骤很多，如果任何一个环节出现问题，就要做响应的处理
1. 用户没有登录就要保存购买信息，并跳转到登录页面
2. 用户状态有问题则直接提示禁止继续购买
3. 如果没有货物则跳转商品页面
4. 同时购买人太多，自己购买时无货

### 处理思路
这个时候该如何实现这个流程？
1. 写到一个  controller  里面，顺序执行，哪一步出错直接  return  ? 这个  controller  该有多长，代码完全不可读，这是典型面向过程了。
2. 封装几个业务方法返回  true   false  判断？比第一个好，但是就像编辑器多了折叠功能，其实还是面向过程的思路。
是时候定义一个处理这个 购买流程的类 和一些 异常 了。下面是每个步骤的分析
1. 需要在中间件验证用户是否登录，直接跳转。
2. 可以写个中间件，命名为  BlacklistMiddleware  专门处理黑名单，也是直接跳转到禁止界面。
3. 此时其实已经到我们的业务处理类里面了，如果无货，你还会写跳转到无货页面吗？显然这里不合适了，因为你不知道什么时候需求变更（可以继续购买，只不过等待到货而已），如果真的跟需求变更来回改核心代码，累死也写不完程序了。建一个  NoGoodsException  异常，当你业务处理类发现没有货，直接抛出该异常。然后在控制器中  try catch  捕获该异常进行后续处理，或者使用  App\Exceptions\Handler  进行统一处理。
4. 如果你定义了上面的异常，那么你就尽情的抛出异常吧，已经有程序帮你处理后面的事情了。
这样的好处就是，你的逻辑完全分离，不要再在业务逻辑代码里面考虑如何返回什么页面，要跳转到哪里，只考虑抛出合适的异常即可，简单的可以直接在  App\Exceptions\Handler  定义通用的捕获异常处理方式，这样的表现就非常统一了。如果需求高了，可以  try catch  后再根据情况再抛更详细的异常。

### 实践
```$xslt
public function update($id)
{
    // 过去你可能这么写
    /*
    $post = Post::find($id);
    if ( is_null($post) ) {
        return view('errors.404')->withMessage('没有找到');
    }
    */
    // 现在直接这样写
    $post = Post::findOrFail($id);

    if (Gate::denies('view', $post)) {
        // 过去你可能会这样写
        // return view('errors.401');
        // 现在可以这样写
        throw new AuthorizationException('您不是该文章的作者，不能修改');
    }

    # ...
}
```

```$xslt
// App\Exceptions\Handler.php 
public function render($request, Exception $e)
{
    // 没有权限访问
    if ($e instanceof ForbiddenException) {
        $message      = $e->getMessage() ?: '您没有权限操作';
        $code     = $e->getCode() ?: 401;
        $redirect = $e->getRedirect() ?: route('error.401');

        return $request->ajax() || $request->wantsJson() ? response()->json([ 'message' => $message ],
            $code) : response(view('errors.401', compact('code', 'message', 'redirect')), $code);
    }

    // FirstOrFail 和 FindOrFail 异常处理
    if ($e instanceof ModelNotFoundException) {
        // 如果删除的内容已经不存在了，就没必要报错了，直接成功处理
        if ('DELETE' === strtoupper(Request::method())) {
            return Response::json([ 'success' => true ]);
        }
        if ($request->ajax() || $request->wantsJson()) {
            return response()->json([ 'message' => '没有找到' ], 404);
        } else {
            return response()->view('errors.404', [ ], 404);
        }
    }

    return parent::render($request, $e);
}
```

### 记录异常

对于某些异常，我们可能需要记录下来，以便方便发现问题，在  App\Exceptions\Handler  我们可以不去记录一些异常

安装  Laravel 5 log viewer  ，便于查看异常。
或者使用 Bugsnag 进行异常记录和分析。可参见 @Summer 的 https://laravel-china.org/topics/290

### 总结
 异常 对我们 控制程序的流程 来说非常重要。解耦了程序出现意想不到结果时信息传递的逻辑。每个业务模块发生异常最终通过  Laravel  的方便的异常处理，和友好的展示，并能根据情况来记录错误，这样让我们的程序更加健壮，方便开发和维护。