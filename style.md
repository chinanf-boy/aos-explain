### aos.scss

``` js
import styles from './../sass/aos.scss';
```

### scss

css 的预处理器, 不多说了, 就当自己写着, 有一定逻辑,变量,拼接,包含的语言就好

### aos.scss

``` scss
@import 'core';
@import 'easing';
@import 'animations';
```

### core

`_core.scss`

``` scss
// Generate Duration && Delay
[data-aos] {
  @for $i from 1 through 60 {
    body[data-aos-duration='#{$i * 50}'] &,
    &[data-aos][data-aos-duration='#{$i * 50}'] {
      transition-duration: #{$i * 50}ms;
    }

    body[data-aos-delay='#{$i * 50}'] &,
    &[data-aos][data-aos-delay='#{$i * 50}'] {
      transition-delay: 0;

      &.aos-animate {
        transition-delay: #{$i * 50}ms;
      }
    }
  }
}

```

### easing

`_easing.scss`
<details>


``` scss
$aos-easing: (
  linear: cubic-bezier(.250, .250, .750, .750),

  ease: cubic-bezier(.250, .100, .250, 1),
  ease-in: cubic-bezier(.420, 0, 1, 1),
  ease-out: cubic-bezier(.000, 0, .580, 1),
  ease-in-out: cubic-bezier(.420, 0, .580, 1),

  ease-in-back: cubic-bezier(.6, -.28, .735, .045),
  ease-out-back: cubic-bezier(.175, .885, .32, 1.275),
  ease-in-out-back: cubic-bezier(.68, -.55, .265, 1.55),

  ease-in-sine: cubic-bezier(.47, 0, .745, .715),
  ease-out-sine: cubic-bezier(.39, .575, .565, 1),
  ease-in-out-sine: cubic-bezier(.445, .05, .55, .95),

  ease-in-quad: cubic-bezier(.55, .085, .68, .53),
  ease-out-quad: cubic-bezier(.25, .46, .45, .94),
  ease-in-out-quad: cubic-bezier(.455, .03, .515, .955),

  ease-in-cubic: cubic-bezier(.55, .085, .68, .53),
  ease-out-cubic: cubic-bezier(.25, .46, .45, .94),
  ease-in-out-cubic: cubic-bezier(.455, .03, .515, .955),

  ease-in-quart: cubic-bezier(.55, .085, .68, .53),
  ease-out-quart: cubic-bezier(.25, .46, .45, .94),
  ease-in-out-quart: cubic-bezier(.455, .03, .515, .955)
);

// Easings implementations
// Default timing function: 'ease'

[data-aos] {
  @each $key, $val in $aos-easing {
    body[data-aos-easing="#{$key}"] &,
    &[data-aos][data-aos-easing="#{$key}"] {
      transition-timing-function: $val;
    }
  }
}

```

### animations

`_animations.scss`

``` scss
// Animations variables
$aos-distance: 100px !default;




/**
 * Fade animations:
 * fade
 * fade-up, fade-down, fade-left, fade-right
 * fade-up-right, fade-up-left, fade-down-right, fade-down-left
 */

[data-aos^='fade'][data-aos^='fade'] {
  opacity: 0;
  transition-property: opacity, transform;

  &.aos-animate {
    opacity: 1;
    transform: translate3d(0, 0, 0);
  }
}

[data-aos='fade-up'] {
  transform: translate3d(0, $aos-distance, 0);
}

[data-aos='fade-down'] {
  transform: translate3d(0, -$aos-distance, 0);
}

[data-aos='fade-right'] {
  transform: translate3d(-$aos-distance, 0, 0);
}

[data-aos='fade-left'] {
  transform: translate3d($aos-distance, 0, 0);
}

[data-aos='fade-up-right'] {
  transform: translate3d(-$aos-distance, $aos-distance, 0);
}

[data-aos='fade-up-left'] {
  transform: translate3d($aos-distance, $aos-distance, 0);
}

[data-aos='fade-down-right'] {
  transform: translate3d(-$aos-distance, -$aos-distance, 0);
}

[data-aos='fade-down-left'] {
  transform: translate3d($aos-distance, -$aos-distance, 0);
}




/**
 * Zoom animations:
 * zoom-in, zoom-in-up, zoom-in-down, zoom-in-left, zoom-in-right
 * zoom-out, zoom-out-up, zoom-out-down, zoom-out-left, zoom-out-right
 */

[data-aos^='zoom'][data-aos^='zoom'] {
  opacity: 0;
  transition-property: opacity, transform;

  &.aos-animate {
    opacity: 1;
    transform: translate3d(0, 0, 0) scale(1);
  }
}

[data-aos='zoom-in'] {
  transform: scale(.6);
}

[data-aos='zoom-in-up'] {
  transform: translate3d(0, $aos-distance, 0) scale(.6);
}

[data-aos='zoom-in-down'] {
  transform: translate3d(0, -$aos-distance, 0) scale(.6);
}

[data-aos='zoom-in-right'] {
  transform: translate3d(-$aos-distance, 0, 0) scale(.6);
}

[data-aos='zoom-in-left'] {
  transform: translate3d($aos-distance, 0, 0) scale(.6);
}

[data-aos='zoom-out'] {
  transform: scale(1.2);
}

[data-aos='zoom-out-up'] {
  transform: translate3d(0, $aos-distance, 0) scale(1.2);
}

[data-aos='zoom-out-down'] {
  transform: translate3d(0, -$aos-distance, 0) scale(1.2);
}

[data-aos='zoom-out-right'] {
  transform: translate3d(-$aos-distance, 0, 0) scale(1.2);
}

[data-aos='zoom-out-left'] {
  transform: translate3d($aos-distance, 0, 0) scale(1.2);
}




/**
 * Slide animations
 */

[data-aos^='slide'][data-aos^='slide'] {
  transition-property: transform;

  &.aos-animate {
    transform: translate3d(0, 0, 0);
  }
}

[data-aos='slide-up'] {
  transform: translate3d(0, 100%, 0);
}

[data-aos='slide-down'] {
  transform: translate3d(0, -100%, 0);
}

[data-aos='slide-right'] {
  transform: translate3d(-100%, 0, 0);
}

[data-aos='slide-left'] {
  transform: translate3d(100%, 0, 0);
}




/**
 * Flip animations:
 * flip-left, flip-right, flip-up, flip-down
 */

[data-aos^='flip'][data-aos^='flip'] {
  backface-visibility: hidden;
  transition-property: transform;
}

[data-aos='flip-left'] {
  transform: perspective(2500px) rotateY(-100deg);
  &.aos-animate {transform: perspective(2500px) rotateY(0);}
}

[data-aos='flip-right'] {
  transform: perspective(2500px) rotateY(100deg);
  &.aos-animate {transform: perspective(2500px) rotateY(0);}
}

[data-aos='flip-up'] {
  transform: perspective(2500px) rotateX(-100deg);
  &.aos-animate {transform: perspective(2500px) rotateX(0);}
}

[data-aos='flip-down'] {
  transform: perspective(2500px) rotateX(100deg);
  &.aos-animate {transform: perspective(2500px) rotateX(0);}
}

```


</details>