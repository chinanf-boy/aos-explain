### helper

`aos/src/js/helpers/*`

### calculateOffset

`calculateOffset.js`

从 各个元素 自定义设置 优于 全局设置, 计算 每个元素触发条件高度

<details>

``` js
/**
 * 计算偏移量
 * 基于元素的设置，如：
 * - anchor
 * - offset
 *
 * @param  {Node} el [Dom element]
 * @return {Integer} [将用于触发动画的最终偏移量位置良好]
 */

import getOffset from './../libs/offset';

const calculateOffset = function (el, optionalOffset) {
  let elementOffsetTop = 0;
  let additionalOffset = 0;
  const windowHeight = window.innerHeight;
  const attrs = {
    offset: el.getAttribute('data-aos-offset'),
    anchor: el.getAttribute('data-aos-anchor'),
    anchorPlacement: el.getAttribute('data-aos-anchor-placement')
    // 锚点放置 - 屏幕上元素的哪一个位置应触发动画 default: top-bottom
  };

  if (attrs.offset && !isNaN(attrs.offset)) { // 自身偏移量
    additionalOffset = parseInt(attrs.offset);
  }

  if (attrs.anchor && document.querySelectorAll(attrs.anchor)) { 
    // 以 哪个元素 作为 自身高度目标
    el = document.querySelectorAll(attrs.anchor)[0];
  }

  elementOffsetTop = getOffset(el).top; // 本身元素位置高度

  switch (attrs.anchorPlacement) { // 自身高度的加减
    // 第一个 变量 指的是 元素的位置 top/bottom/center
    // 第二个 变量  指的是 窗口的位置 top/bottom/center
    // top-bottom 的意思是
    // 当 元素的上边 到 窗口 底部 时
    case 'top-bottom':
      // Default offset
      break;
    case 'center-bottom':
      elementOffsetTop += el.offsetHeight / 2;
      break;
    case 'bottom-bottom':
      elementOffsetTop += el.offsetHeight;
      break;
    case 'top-center':
      elementOffsetTop += windowHeight / 2;
      break;
    case 'bottom-center':
      elementOffsetTop += windowHeight / 2 + el.offsetHeight;
      break;
    case 'center-center':
      elementOffsetTop += windowHeight / 2 + el.offsetHeight / 2;
      break;
    case 'top-top':
      elementOffsetTop += windowHeight;
      break;
    case 'bottom-top':
      elementOffsetTop += el.offsetHeight + windowHeight;
      break;
    case 'center-top':
      elementOffsetTop += el.offsetHeight / 2 + windowHeight;
      break;
  }

  if (!attrs.anchorPlacement && !attrs.offset && !isNaN(optionalOffset)) {
    // 元素 自身偏移量的设置级别 优于 全局偏移量
    additionalOffset = optionalOffset;
  }

  return elementOffsetTop + additionalOffset; // 触发条件的高度准备好了
};

export default calculateOffset;

```

- [getOffset](./lib.md#offset)

拿到 元素 离`上`面的绝对值和离`左`面的绝对值


</details>

### detect

`detector.js`

平台设备匹配三大类, 手机/移动/平板


<details>

``` js
/**
 * Device detector
 */

const fullNameRe = /(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i;
const prefixRe = /1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i;
const fullNameMobileRe = /(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino|android|ipad|playbook|silk/i;
const prefixMobileRe = /1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i;


function ua() {
  return navigator.userAgent || navigator.vendor || window.opera || '';
  // 获取 设备 信息
}


class Detector {

  phone() {
    const a = ua();
    return !!(fullNameRe.test(a) || prefixRe.test(a.substr(0, 4)));
    // 测试 有没有 匹配手机信息
  }

  mobile() {
    const a = ua();
    return !!(fullNameMobileRe.test(a) || prefixMobileRe.test(a.substr(0, 4)));
    // 测试 有没有 匹配移动信息
    
  }

  tablet() {
    return this.mobile() && !this.phone();
    // 是移动但不是手机
  }
};

export default new Detector;

```

</details>



### elements

`elements.js`

拿到 __document__ 下 `所有`具有 `data-aos` 属性的`节点数组`

<details>

``` js
/**
 * 生成元素作为对象的初始数组
 * 此数组稍后将使用元素属性值进行扩展
 * 像 'position'
 */
const createArrayWithElements = function (elements) {
  elements = elements || document.querySelectorAll('[data-aos]');
  return Array.prototype.map.call(elements, node => ({ node }));
};

export default createArrayWithElements;

```

</details>

### handleScroll

`handleScroll.js`

滚动计算 - 对每个元素 加减 'aos-animate' class 

<details>

``` js
/**
 * 加减aos-animate class
 * @param {node} el         element
 * @param {int}  top        scrolled distance
 * @param {void} once
 */
const setState = function (el, top, once) {
  const attrOnce = el.node.getAttribute('data-aos-once'); 

  // default
  // typeof attrOne == typeof null
  // "object" so 👀
  
  if (top > el.position) { // 进入 node 范围
    el.node.classList.add('aos-animate');
  } else if (typeof attrOnce !== 'undefined') { // 离开 node 范围
    if (attrOnce === 'false' || (!once && attrOnce !== 'true')) { // once 一次动画的不需要移除
      el.node.classList.remove('aos-animate');
    }
  }
};


/**
 * 滚动计算 - 加减 'aos-animate' class on scroll
 *
 * @param  {array} $elements         array of elements nodes
 * @param  {bool} once               plugin option
 * @return {void}
 */
const handleScroll = function ($elements, once) {
  const scrollTop = window.pageYOffset;
  const windowHeight = window.innerHeight;
  /**
   * 检查所有注册的元素位置
   * 和滚动动画
   */
  $elements.forEach((el, i) => {
    setState(el, windowHeight + scrollTop, once);
  });
};

export default handleScroll;

```

</details>

### prepare

`prepare.js`


为每个元素 加 `aos-init` , 和 逐个计算 元素触发动画条件高度

<details>

``` js
/* 清除变量 */

import calculateOffset from './calculateOffset';

const prepare = function ($elements, options) {
  $elements.forEach((el, i) => {
    el.node.classList.add('aos-init'); // 增加 aos-init class
    el.position = calculateOffset(el.node, options.offset); // 自定义 元素高度+ 偏移量
  });
  return $elements;
};

export default prepare;

```

- [x] [calculateOffset](#calculateoffset)

从 各个元素 自定义设置 优于 全局设置, 计算 每个元素触发条件高度


</details>
