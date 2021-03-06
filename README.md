# Learning Jquery Validate #

1. dom元素的name属性是必须的，而且通常需保证id和name具有相同值
2. 一些不符合javascript标准的name值需要在rule中指出
```
 $("#myform").validate({
   rules: {
     // no quoting necessary
     name: "required",
     // quoting necessary!
     "user[email]": "email",
     // dots need quoting, too!
     "user.address.street": "required"
   }
 });
```
3. 对于一些具有相同验证规则的input，可以不必劳烦为每个input都单独加入验证规则，jQuery validate允许包装原有的验证方法，并且使用`$.validator.addClassRule`来将自定义的验证方法应用到具有相应class的元素上
```
 // alias required to cRequired with new message
 $.validator.addMethod("cRequired", $.validator.methods.required,
   "Customer name required");
 // alias minlength, too
 $.validator.addMethod("cMinlength", $.validator.methods.minlength,
   // leverage parameter replacement for minlength, {0} gets replaced with 2
   $.validator.format("Customer name must have at least {0} characters"));
 // combine them both, including the parameter for minlength
 $.validator.addClassRules("customer", { cRequired: true, cMinlength: 2 });
```
4. 你自己实现验证方法的时候也可以调用系统自带的方法，比如，`jQuery.validator.methods.email.call(this, email, element)`
5. 错误信息显示有四种方式
	- title属性
	- data-msg-[validator]属性
	- 通过错误标签（lavel）
	- 通过option来指定
6. validator可以直接写成dom元素的属性，前面并不需要加一个`data-`, 比如可以直接`<input type="text" name="password_again" equalto>`而且validator并不需要按照驼峰的写法。
7. The library adds three jQuery plugin method:
	- validate() 主要用来初始化验证，配置相关的验证选项
	- valid() 返回一个bool值，用来指示表单或者单个元素是否valid，除此之外，还将显示相应的错误提示信息
	- rules() Read, add and remove rules for an element, 特别注意，rules函数针对的是单个元素，或者元素集合
	- 每条rule其实就是validator及其取值组成，每个rule对应有自己的message, 但是message并不和rule结合，而是统一在一个message:{}这样的结构中列出。
8. 最重要的一个函数实际上就是validate函数，一切的验证规则都可以在这里配置，当然也可以通过dom的属性来配置，配置项：
	- debug开启调试，也就是说form并不真正被提交到服务器(true/false)
	- submitHandler需要指定一个回调函数, `function(form){$(form).ajaxSubmit()}`, 注意, $(form).ajaxSubmit()这是一种比较好的异步提交的方式，如果使用$(form).submit()会造成循环验证，因此这种方法在submitHander中不能使用， 而应该使用`form.submit()` 
	- invalidHandler当form验证失败的时候调用该函数，通过传入的两个参数，event，validator可以查看验证失败的相关提示信息。
	- ignore这是一个选择器，表示内部使用了jQuery的not选择器，指示哪些元素在验证的时候被忽略。
	- rules
	- messages
	- groups指定一组错误消息，这一组的出错消息会被统一显示，配合errorPlacement来确定错误消息被显示的位置
	- onsubmit可以设置为false表示提交表单的时候并不执行validate, 也可以指定为一个function，自己来确定合适的执行逻辑
	- onfocusout可以用来设置某一元素执行验证的时机，注意是单个元素
	- onkeyup As long as the field is not marked as invalid, nothing happens. Otherwise, 每一次按键弹起都会被检测
	- wrapper 为error label包裹一层指定的标签
	- errorLabelContainer 标签不再被加在input后面，而是单独放在errorLabelContainer里面
	- errorContainer 看不出有什么作用，只是会在表单invalid的时候显示
	- showErrors A custom message display handler, first argument is the map of errors, the second argument is an array or errors.
		- errorMap key/value pair, where the key refers to the name of the input, values the message to be displayed for that input. 
		- errorList [{message, element}...]
	- errorPlacement(function) 默认的话error标签会被加到input后面，handler的第一个参数是创建好的error label(jquery object), 第二个参数是报错的元素(jquery object), 有了这两个元素我们就可以对错误标签显示的位置进行自定义处理了
	- success如果input通过验证，那么，同样会显示一个error label，可以是一个回调，也可以是一个string（用来给error label添加一个class）
	- highlight/unhilight用来醒目标记出错的元素
9. $.validator.addMethod `jQuery.validator.addMethod( name, method [, message ] )`, 其中method 接收两个参数，第一个为value, 第二个为被验证的元素，第三个是出错信息。可以通过$.validator.addClassRules批量为具有某个类的元素批量添加验证规则("class", {rule})
