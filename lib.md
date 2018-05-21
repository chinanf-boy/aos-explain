### libs

`aos/src/js/libs/*`

### offset

`offset.js`

拿到 元素 离`上`面的绝对值和离`左`面的绝对值

``` js
/**
 * 获取DOM元素助手的偏移量
 * 包括 这些与 translation
 *
 * @param  {Node} el [DOM element]
 * @return {Object} [top and left offset]
 */
const offset = function (el) {
  let _x = 0;
  let _y = 0;

  while (el && !isNaN(el.offsetLeft) && !isNaN(el.offsetTop)) {
    _x += el.offsetLeft - (el.tagName != 'BODY' ? el.scrollLeft : 0);
    _y += el.offsetTop - (el.tagName != 'BODY' ? el.scrollTop : 0);
    el = el.offsetParent;
  }
  // 递归 父辈 

  return {
    top: _y, // 离上面的绝对值
    left: _x // 离左面的绝对值
  };
};

export default offset;

```

### observe

`observer.js`

调用 浏览器的观察 对象, 如果 `aos` 的节点改动, 重启

``` js
const doc = window.document;
const MutationObserver =
  window.MutationObserver ||
  window.WebKitMutationObserver ||
  window.MozMutationObserver;

let callback = () => {};

function ready(selector, fn) {
  const observer = new MutationObserver(check);
  callback = fn;

  observer.observe(doc.documentElement, {
    childList: true, // 如果需要观察目标节点的子节点
    subtree: true, // 如果还需要观察目标节点的所有后代节点
    removedNodes: true // 返回被删除的节点 ??
  });
}

// https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver#Example_usage




function check(mutations) { // 观察 整个 html 文档
  if (!mutations) return;

  mutations.forEach(mutation => {
    const addedNodes = Array.prototype.slice.call(mutation.addedNodes); // 返回被添加的节点
    const removedNodes = Array.prototype.slice.call(mutation.removedNodes); // 返回被删除的节点

    const anyAOSElementAdded = addedNodes
      .concat(removedNodes)
      .filter(el => el.hasAttribute && el.hasAttribute('data-aos'))
      .length;

    if (anyAOSElementAdded) { // 当 aos 的 节点被 增加或删除时, 重启 aos
      callback();
    }
  });
}

export default ready;

```