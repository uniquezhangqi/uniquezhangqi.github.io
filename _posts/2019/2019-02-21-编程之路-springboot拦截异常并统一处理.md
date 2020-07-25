---
layout:     post             				# 使用的布局（不需要改）
title:         springboot拦截异常并统一处理       # 标题 
subtitle:    					  				#副标题
date:       2019-02-23  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
---

> 转载 神奇Sam的博客：https://my.oschina.net/magicalSam/blog/1456337


在spring 3.2中，新增了@ControllerAdvice 注解，可以用于定义@ExceptionHandler、@InitBinder、@ModelAttribute，并应用到所有@RequestMapping中

## 创建 MyControllerAdvice，并添加 @ControllerAdvice注解。

```java

@ControllerAdvice
public class MyControllerAdvice {

    /**
     * 应用到所有@RequestMapping注解方法，在其执行之前初始化数据绑定器
     * @param binder
     */
    @InitBinder
    public void initBinder(WebDataBinder binder) {}

    /**
     * 把值绑定到Model中，使全局@RequestMapping可以获取到该值
     * @param model
     */
    @ModelAttribute
    public void addAttributes(Model model) {
        model.addAttribute("author", "Magical Sam");
    }

    /**
     * 全局异常捕捉处理
     * @param ex
     * @return
     */
    @ResponseBody
    @ExceptionHandler(value = Exception.class)
    public Map errorHandler(Exception ex) {
        Map map = new HashMap();
        map.put("code", 100);
        map.put("msg", ex.getMessage());
        return map;
    }

}

```
启动应用后，被 @ExceptionHandler、@InitBinder、@ModelAttribute注解的方法，都会作用在 被@RequestMapping注解的方法上。

@ModelAttribute：在Model上设置的值，对于所有被@RequestMapping注解的方法中，都可以通过 ModelMap 获取，如下：

```java
@RequestMapping("/home")
public String home(ModelMap modelMap) {
    System.out.println(modelMap.get("author"));
}

//或者 通过@ModelAttribute获取

@RequestMapping("/home")
public String home(@ModelAttribute("author") String author) {
    System.out.println(author);
}

```


@ExceptionHandler 拦截了异常，我们可以通过该注解实现自定义异常处理。其中，@ExceptionHandler 配置的 value 指定需要拦截的异常类型，上面拦截了 Exception.class 这种异常。


## 二、自定义异常处理（全局异常处理）
```java
public class MyException extends RuntimeException {
 private String code;
 private String msg;
 
 // private T date;

    public MyException(String code, String msg) {
        this.code = code;
        this.msg = msg;
    }
    // getter & setter
}

```

```java

@ControllerAdvice
public class MyControllerAdvice {

    /**
     * 全局异常捕捉处理
     * @param ex
     * @return
     */
    @ResponseBody
    @ExceptionHandler(value = Exception.class)
    public Map errorHandler(Exception ex) {
        Map map = new HashMap();
        map.put("code", 100);
        map.put("msg", ex.getMessage());
        return map;
    }
    
   	/**
     *  拦截捕捉自定义异常 MyException.class
     * @param ex
     * @return
     */
    @ResponseBody
    @ExceptionHandler(value = MyException.class)
    public Map myErrorHandler(Exception e) {
        Map map = new HashMap();
    	if(e instanceof MyException){
        	map.put("code", e.getCode());
        	map.put("msg", e.getMsg());
        	return map;
    	}
        logger.error("[系统异常]：e");
        map.put("code", 50000);
        map.put("msg", "系统内部发生异常！");
        return map;
    }
}
```



`文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！`


