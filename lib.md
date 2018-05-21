### libs

`aos/src/js/libs/*`

### offset

`offset.js`

``` js
/**
 * Get offset of DOM element Helper
 * including these with translation
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

  return {
    top: _y,
    left: _x
  };
};

export default offset;

```

### observe

`observer.js`

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
    childList: true,
    subtree: true,
    removedNodes: true
  });
}

function check(mutations) {
  if (!mutations) return;

  mutations.forEach(mutation => {
    const addedNodes = Array.prototype.slice.call(mutation.addedNodes);
    const removedNodes = Array.prototype.slice.call(mutation.removedNodes);

    const anyAOSElementAdded = addedNodes
      .concat(removedNodes)
      .filter(el => el.hasAttribute && el.hasAttribute('data-aos'))
      .length;

    if (anyAOSElementAdded) {
      callback();
    }
  });
}

export default ready;

```