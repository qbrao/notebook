<!-- TOC -->

- [WEB 表单设计——最佳实践、组件源码、应用场景](#web-表单设计最佳实践组件源码应用场景)
  - [WEB 表单设计最佳实践](#web-表单设计最佳实践)
  - [Element UI 源码分析](#element-ui-源码分析)
    - [`FormItem` 组件](#formitem-组件)
    - [`Form` 组件](#form-组件)
  - [应用场景](#应用场景)

<!-- /TOC -->

# WEB 表单设计——最佳实践、组件源码、应用场景

这篇文章是为 4 月份的公司内部分享而写，涉及面比较广，所以很多东西没有细说。

## WEB 表单设计最佳实践

> 可用性工程师、网站开发人员、产品经理、视觉设计师、交互设计师或者信息架构人员——无论哪种设计师——其工作都会以某种形式涉及到表单设计。

**表单元素**

表单具备哪些最基本的元素？

- 标签（提出问题）
- 输入框（回答问题）
- 动作（提交答案）
- 帮助文字（解释问题或答案）
- 错误与成功（及时反馈）

**标签**

- 标签有几种对齐方式？各种对齐方式的优缺点是什么？能否使用混合对齐方式？
- 标签文案需要注意什么？标签末尾是否需要冒号？
- 哪些问题要设为必填项？那些问题可以不用问或者推迟问？“保留，删减，延迟，解释”，形成更好问题的四大策略。

**输入框**

- 如何设置输入框的宽度？不同输入框的长度能给用户什么暗示？什么情况下输入框的长度需要保持一致？
- 问题的默认答案——默认值。那些答案需要默认值？如何设置默认值？
- 应该使用什么类型的组件来回答问题？`select` 和 `radio` 都能单选如何选择？

**动作**

- 主要动作和次要动作有什么区别？表现形式上有什么差异？如何放置？
    - 主要动作。（Submit）提交、（Continue）继续、(Next)下一步
    - 次要动作。（Cancel）取消、（Back）返回、(Previous)上一步
- 合适的按钮文案？确定、提交、保存、确认修改、同意并购买等等文案我们如何选择？
- 表单需要回答的问题过多时分步填写还是分页填写？需要注意什么？

**帮助文字**

- 什么情况下需要帮助文字？
- 简洁或复杂的文字展现方式有何不同？
- 帮助文字应该放在什么位置？
- 帮助文字的展示时机？

**其他**

- 为什么有些网站的表单填写起来会轻松，不知不觉就填完了。有些则恰恰相反？
- 指纹解锁、人脸识别等交互方式正在代替传统的表单输入，若干年以后当前使用的表单填写方式会消失吗？

上面列出了一些关于表单常见的的问题，有些是我以前想到但是找不到答案的问题，有些是我根本没有发现的问题。当然这些不是全部。当拿到交互和UI设计稿的时候，作为一个前端开发是否有很多疑问？为什么要这样设计？这样设计对吗？有没有更好的解决方案？在[《WEB表单设计——点石成金的艺术》](http://cdn.lxqnsys.com/WEB%E8%A1%A8%E5%8D%95%E8%AE%BE%E8%AE%A1%20%20%E7%82%B9%E7%9F%B3%E6%88%90%E9%87%91%E7%9A%84%E8%89%BA%E6%9C%AF.pdf)这本书中统统都能找到答案。没有去总结归纳这些问题的答案是因为这本书写的很简洁明了，薄薄的一本也很好读，每一章结尾都有最佳实践的总结。我觉得最重要的是有这方面的意识，不知道自己不知道和知道自己不知道是两种不同的学习阶段，有不同的体验。

> 打破规则之前，先要知道规则是什么。

前端开发中我们会遵循一些编程原则，接受一些编程设计模式的指导来完成编码。这本书中同样给出了 WEB 表单交互的一些原则和设计模式。何不站在巨人的肩膀上，让我们走的更远。当透过表象看到本质后，一切都会变得的清晰。

作为一个前端开发人员需要知道这些吗？如果知道这些，当交互图出来后，我们可以快速理解对方的意图，通过 WEB 表单最佳实践发现设计缺陷和错误，接下来就能很顺畅的完成前端工作。

## Element UI 源码分析

Element UI 中的 `Form` 组件中有 `Form`、`FormItem`、`LabelWrap` 三个组件。暴露给用户使用的是前面两个组件，`LabelWrap` 在设置 `label-width="auto"` 自适应宽度的时，会将表单中所有 Label 的宽度放到 `Form`组件 的 `potentialLabelWidthArr` 数组中，用来获取最大的那个宽度。这里的写法很有意思可以学习一下，但是源码中使用 `float` 来实现比 `flex` 实现复杂很多。

### `FormItem` 组件

表单组件中几乎所有功能都在 `FormItem` 中实现，当使用 `Form` 组件中暴露的方法时，其实是在调用或者遍历调用 `FormItem` 组件的方法。

**data()**

- `initialValue`。用来存放表单元素的初始值，在调用 `resetField()` 方法时用来重置初始值。`initialValue` 会在 `mounted` 钩子中被设置。所以初始值应该在 `mounted` 之前设置才会达到预期的效果。

**computed**

- `labelStyle` 用来设置 label 元素的宽度，`labelPosition === 'top' || inline` 时则不设置宽度，跟交互规则相对应。
- `contentStyle` 跟 `labelStyle` 类似。
- `form` 设置 `isNested` 属性用来判断当前 `FormItem` 是否被其他 `FormItem` 嵌套，并且返回最近的 `Form` 组件。从这里可以看出 `Form` 和 `FormItem` 两个组件可以无限套娃，这一点很重要。
- `fieldValue` 通过 `FormItem` 设置的 `prop` 属性获取到 `Form` 组件中 `model` 属性对应的表单（input、select等）的值。也就是表单元素的值（input元素中用户输入的值）。
- `isRequired` 用来判断当前 `formItem` 是否是必填项。当前 `FormItem` 只要有一条规则中有配置为必填项，则这个 `FormItem` 就是必填项。

**methods**

- `getRules()` 用来获取当前 `FormItem` 的验证规则。将 `FormItem` 配置的 `required` 属性格式化，`FormItem` 权重大于（覆盖） `Form` 的 `rules` 配置。
- `getFilteredRule(trigger)`。`FormItem` 可以配置多条验证规则，这个方法通过 trigger（blur、change）获取需要验证的那条或多条规则，最终返回的是一个数组。当规则中没有设置 `trigger` 时一定会验证。
- `validate()`，我们通常会用到 `Form` 的 `validate()`，`FormItem` 的 `validate()` 用来验证单个表单元素的值，使用的场景相对少一些。

```js
validate(trigger, callback = noop) {
  this.validateDisabled = false;
  const rules = this.getFilteredRule(trigger);

  if ((!rules || rules.length === 0) && this.required === undefined) {
    callback();
    return true;
  }
  this.validateState = 'validating';

  const descriptor = {};
  // 删除每个验证规则的 trigger 属性，让 AsyncValidator 验证每一条规则
  if (rules && rules.length > 0) {
    rules.forEach(rule => {
      delete rule.trigger;
    });
  }
  descriptor[this.prop] = rules;

  const validator = new AsyncValidator(descriptor);
  const model = {};

  model[this.prop] = this.fieldValue;

  validator.validate(model, { firstFields: true }, (errors, invalidFields) => {
    this.validateState = !errors ? 'success' : 'error';
    this.validateMessage = errors ? errors[0].message : '';

    callback(this.validateMessage, invalidFields);
    // 当每一条规则验证完成后，在 Form 组件上 emit validate事件，返回最终的验证结果
    this.elForm && this.elForm.$emit('validate', this.prop, !errors, this.validateMessage || null);
  });
},
```

- `getPropByPath()` 这个方法在表单组件中用，其它组件很少用到。

```js
/**
 * 通过路径获取对象中想要属性
 * @param {Object} obj 对象
 * @param {String} path 获取对象值的路径
 * @param {Boolean} strict 是否开启严格模式，开启后如果路径中间的属性没有获取到值则会报错，最后一个值获取不到不会报错
 * @returns
 */
function getPropByPath(obj, path, strict) {
  let tempObj = obj;
  path = path.replace(/\[(\w+)\]/g, '.$1');
  path = path.replace(/^\./, '');

  let keyArr = path.split('.');
  let i = 0;
  for (let len = keyArr.length; i < len - 1; ++i) {
    debugger;
    if (!tempObj && !strict) break;
    let key = keyArr[i];
    if (key in tempObj) {
      tempObj = tempObj[key];
    } else {
      if (strict) {
        throw new Error('please transfer a valid prop path to form item!');
      }
      break;
    }
  }
  return {
    o: tempObj,
    k: keyArr[i],
    v: tempObj ? tempObj[keyArr[i]] : null
  };
};

// 手动验证一下输入和输出
var obj = {
  a: {
    b: {
      c: 1,
    }
  }
}

var result1 = getPropByPath(obj, "a.b.c");
console.log(result1);
// 最终 result1 的结果为
// {
//   o: {c: 1},
//   k: "c",
//   v: 1,
// }

var result2 = getPropByPath(obj, "a.b.e");
console.log(result2);
// 最终 result2 的结果为
// {
//   o: {c: 1},
//   k: "e",
//   v: undefined,
// }

var result3 = getPropByPath(obj, "a.e.c");
console.log(result3);
// 最终 result3 的结果为
// {
//   o: {
//     b: {
//       c: 1,
//     }
//   },
//   k: "e",
//   v: undefined,
// }

var result4 = getPropByPath(obj, "a.e.c", true);
// 会抛出错误
```

- emitter mixins 中的 `broadcast` 和 `dispatch` 用法。

这两个方法在 Element UI 中经常会用到，[Vue框架Element的事件传递broadcast和dispatch方法分析](https://www.cnblogs.com/xxcanghai/p/6382607.html)这篇文章写分析的很好，就不多说了。

总结一下用法：`broadcast` 向所有**指定**的子孙后代组件传递事件，`dispatch` 向所有**指定**的祖先长辈组件传递事件。

### `Form` 组件

`Form` 组件的 `fields` 属性用来保存所有放在其中的 `FormItem` 的实例。当调用 `resetFields`、`clearValidate`、`validate`、`validateField` 这些暴露给用户的方法时，其实是在遍历 `FormItem` 组件中的方法，这样让使用者用起来更方便。

`validate` 方法若不传入回调函数，则会返回一个 promise, 源码是支持的，在业务代码中可以少写一层嵌套。

`Form` 的源码很简单，没有啥好细说的。

## 应用场景

我在日常工作中遇到的一些问题和场景：

1. 在使用 `Form` 组件的 `resetFields()` 方法重置初始值时，组件是如何实现的？何时设置默认初始值才能生效？
2. 为什么有时候使用 `resetFields()` 方法只有部分表单元素生效？
3. `Form` 组件 和 `FormItem` 组件可以任意嵌套吗？是如何实现的？有哪些应用场景？
4. 表单元素(`input`、`select` 等)一定要放在 `FormItem` 组件中吗？
5. `Form` 的 `rules` 验证规则配置中 `trigger` 能配置哪些值？自定义组件如何触发 `trigger`？

表单在实际业务中的设计可能会很复杂，会导致代码写的很混乱不优雅，排除交互设计不合理的因素，非常重要的一个原因是对表单组件的理解不是很清晰。

有些时候我们需要从后端获取到表单的默认值，所以需要在 `mounted` 之前设置表单的初始值，这一点很重要，因为我们可以通过 `Form` 组件的 `resetFields()` 这个简单和明确的方法来重置到默认值。不然就需要写一个对象来重置默认值，这样做有很多坏处。如果发现无法重置到初始值，大概率是没有设置 `FormItem` 组件的 `prop` 属性。

`FormItem` 可以嵌套 `FormItem`， `FormItem` 也可以嵌套 `Form`。当表单交互特别复杂时，我们可以通过这种嵌套的方式来抽离出 `Form` 中的 `Form`。

`FormItem` 跟表单元素（input等）其实没有必然的嵌套关系。表单元素放在任何地方都行，只需要给 `FormItem` 设置对应的 `prop` 属性即可。

`rules`验证规则中的 `trigger` 只能对 Element UI 封装的组件生效，特定场景下需要在（blur和 change）事件触发后进行验证时，只需要这样调用 `this.dispatch('ElFormItem', 'el.form.change', [val]);` 即可。