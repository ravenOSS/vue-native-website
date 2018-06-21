---
title: Animations 
type: guide
order: 11
vue_version: 2.5.13
gz_size: "30.67"
---

Animations are a great way to enrich the user experience. They are not only a visual treat but makes the touch and "feel" of the app much superior. Let's take a look at how we can create and construct some of the basic animations in Vue Native.

Before we start off with the code, let's take some time to understand some of the concepts we are using to build our animation. Feel free to skip these if you have already come across these in either React Native or Vue.

## Introduction

### DOM and Vue instance's data binding :

The data you use under the `data` function property is bound to the one used in the template section. Meaning manipulating this data, enables the corresponding element in the template section to be (re)rendered.

For Example consider these `template` and `script` sections:

```js
<template>
  <view>
    <text>
      Message: {{ msg }}
    </text>
  </view>
</template>

<script>
import { Text, View } from "react-native";
export default {
  data: function() {
    return {
      msg: "Vue Native!"
    };
  }
};
</script>
```

In the above code snippet, changing the `msg` property would (re)render of the `text` component.

It is important to understand this concept here because when we provide the animation values, this feature enables us to not use setState as we do in React Native and the manipulation of the DOM happens under the hood.

### React Native Animated API

To animate our components we are going to make use of the Animated API provided by the `react-native` package.

Some of the important Animation types used in this article :

#### Animated.timing()

This is the most commonly used Animation type.It supports animating a value over time using one of various predefined easing functions, or you can use your own. To get an idea of it must be used :

```
Animated.timing(msg, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
}).start();
```

#### Composing Animations

Animations can be combined and played in sequence or in parallel.

• Sequence and Parallel types

Sequential animations can play immediately after the previous animation has finished, or they can start after a specified delay.

Parallel animations, like their name suggests, executes the animations provided parallely. If one animation is stopped or interrupted, then all other animations in the group are also stopped.

```
Animated.sequence([
  Animated.parallel([
    // after decay, in parallel:
    Animated.spring(position, {
      toValue: {x: 0, y: 0}, // return to start
    }),
    Animated.timing(twirl, {
      // and twirl
      toValue: 360,
    }),
  ]),
]).start();
```

#### Interpolation

Another important feature we are going to be using is the `Interpolation` .
Each property can be run through an interpolation first. An interpolation maps input ranges to output ranges, typically using a linear interpolation but also supports easing functions.

A simple mapping to convert a 0-1 range to a 0-100 range would be:

```
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

## General file structure (\*.vue)

```js
<template>
<!-- All your UI components go here -->
</template>

<script>
<!-- Import statements go here -->

export default {
  data: function() {
    return {
      //Data declarations

    };
  },
  created: function() {
    //Data can be intialised

  }
  components: {
    //All the components used in template section must be mentioned here
  },
  methods: {
    animate: function() {
      //Our animation function
    }
  }
};
</script>

<style>
<!-- Well, all the styling happens here. -->
</style>
```

## Animations in Vue-Native using Animated API

Now that we have established all the building blocks needed to start animating your components in Vue-Native, let's dive into some animations.

For each of these Animations, I will be following a similar simple pattern which contains updating these parts:

• The style properties which hold the values that are changed by Animated APIs.
• The data section which holds all the properties that are used during Animation.
• Functions that we will create for each animation , residing under the `script` section.

We will be importing these basic necessary components from the `react-native` package and also we will be making use of the `Animated.view` :

```js
<script>
import { Text, View, Animated , Easing } from "react-native";
const animatedView = Animated.View;
const animated = Animated;

export default {
components: {
    Text,
    View,
    animated,
    animatedView,
    Easing,
  },
}
</script>
```

The imported components must be specified under the components section to be used in our `template`.

### The Grow Effect

The `template` section consists of an `animated.view` with style that consists of variable associated with animation.

```html
<template>
  <view :style="{justifyContent:'center',flex:1}">
     <animatedView
        :style="{
          height: grow,
          width: grow ,
          borderRadius:grow,
          backgroundColor: '#008AE7',
          alignSelf:'center'
          }"
          />
</view>
</template>
```

`grow` is the variable that we are going to change and give the values to and it is defined as shown below. Also we make use of several functions provided by Vue such as `created` to initialise our values, `data` function to define the data that will be used in our templates and functions, `mounted` to call the function to animate our component, `components` consists of all the components used in the `template` and finally the `methods` sections which contain all our functions.

```js
<script>
export default {
  data: function() {
    return {
      growValue: 0,
      grow: 0,
    };
  },
  created: function() {
    this.growValue = new animated.Value(0);
    this.animatedValueRotate = new animated.Value(0);
  },
  mounted: function() {
    this.animateGrow();
  },
  components: {
    View,
    animatedView,
    animated,
    Image,
    Easing
  },
  methods: {
    animateGrow: function() {
      this.growValue.setValue(0);
      animated
        .timing(this.growValue, {
          toValue: 1,
          duration: 1000,
          easing: Easing.linear
        })
        .start(() => {
          this.animateGrow();
        });
      this.grow = this.growValue.interpolate({
        inputRange: [0, 0.5, 1],
        outputRange: [0, 200, 0]
      });
    }
  }
};
</script>
```

From the above code, we can see a function named `animateGrow` which makes use of the Animated APIs we spoke about earlier.

The callback used inside the `start` function is used to repeat the animation.

The `interpolate` is used to interpolate the values corresponding to the `input` and `output` range.

As the value of `grow` changes, it is updated on the UI because of the DOM and Vue instance data binding I had talked about earlier.

### Spin and Glide Animation

The animation properties in our `animatedView` component is `marginLeft` and `rotate` property of `tranform`.

```html
<template>
  <view :style="{justifyContent:'center',flex:1}">
     <animatedView
        :style="{
          marginLeft: movingMargin,
          height: 100,
          width: 100 ,
          backgroundColor: 'purple',
          transform: [{rotate: spin}],
          }"
          />
</view>
</template>
```

Our `data` and `created` functions :

```js
data: function() {
    return {
      spinValue: 0,
      spin: "0deg",
      animatedValueRotate: 0,
      movingMargin: 0
    };
  },
  created: function() {
    this.spinValue = new animated.Value(0);
    this.animatedValueRotate = new animated.Value(0);
  },
```

The `animateRotate` function makes use of the the `animated.sequence` and `animated.parallel` types we spoke in the first section :

```js
animationRotate: function() {
      console.log(width);
      this.spinValue.setValue(0);
      this.animatedValueRotate.setValue(0);

      animated
        .sequence([
          animated.parallel([
            animated.timing(this.spinValue, {
              toValue: 1,
              duration: 2000,
              easing: Easing.linear
            }),
            animated.timing(this.animatedValueRotate, {
              toValue: 1,
              duration: 2000,
              easing: Easing.linear
            })
          ]),
          animated.parallel([
            animated.timing(this.spinValue, {
              toValue: 0,
              duration: 2000,
              easing: Easing.linear
            }),
            animated.timing(this.animatedValueRotate, {
              toValue: 0,
              duration: 2000,
              easing: Easing.linear
            })
          ])
        ])
        .start(() => {
          this.animationRotate();
        });

      this.spin = this.spinValue.interpolate({
        inputRange: [0, 1],
        outputRange: ["0deg", "360deg"]
      });

      this.movingMargin = this.animatedValueRotate.interpolate({
        inputRange: [0, 1],
        outputRange: [0, width]
      });
    }
```

The `spin` value interpolates from 0 to 360 and the `movingMargin` from 0 to the width of the screen.

### Flip and Color change animation

The animated properties are :

• marginLeft
• marginTop
• backgroundColor
• transform

```html
<template>
  <view :style="{padding:40}">
     <animatedView
        :style="{
          marginLeft: marginLeft,
          marginTop: marginTop,
          height: 100,
          width: 100,
          backgroundColor: interpolateColor,
          transform: [{rotateY},{rotateX}]
          }"
          />
</view>
</template>
```

Diving into the animation function. Let's first build the first half of the animation and then the other half is just reversing the same.

```js
animated
        .sequence([
          animated.parallel([
            animated.timing(this.animatedValue, {
              toValue: 1,
              duration: this.duration,
              easing: Easing.linear
            }),
            animated.timing(this.animatedValue1, {
              toValue: 1,
              duration: 1000,
              easing: Easing.linear
            }),
            animated.timing(this.animatedColorValue, {
              toValue: 150,
              duration: 1000
            })
          ]),

          animated.parallel([
            animated.timing(this.animatedValue2, {
              toValue: 1,
              duration: this.duration,
              easing: Easing.linear
            }),
            animated.timing(this.animatedValue1a, {
              toValue: 1,
              duration: 1000,
              easing: Easing.linear
            })
          ]),

            ...

            ...

        ]).start();

        this.marginLeft = 0;
      this.marginTop = 0;
      this.marginLeft = this.animatedValue.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 200]
      });

      this.rotateY = this.animatedValue1.interpolate({
        inputRange: [0, 1],
        outputRange: ["0deg", "180deg"]
      });

      this.interpolateColor = this.animatedColorValue.interpolate({
        inputRange: [0, 150],
        outputRange: ["rgb(128,0,128)", "rgb(51, 250, 170)"]
      });

      this.marginTop = this.animatedValue2.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 200]
      });

      this.rotateX = this.animatedValue1a.interpolate({
        inputRange: [0, 1],
        outputRange: ["0deg", "180deg"]
      });
```

The above code snippet produces the following : [gif]

Notice how all the `toValue` in each of the `animated.timing` type have value 1.
Now reversing is as easy as making these to 0 and appending it to `animated.sequence`.
The completed animation looks like this : [gif]

### The Loading Animation

The animated property here is `marginBottom`.

Since all four animations need to happen with some sort of delay and because we are using `stagger` type, we will have differnt marginBottom values for each of these components.

```html
<template>
  <view class="container" :style="{flexDirection:'column',justifyContent:'center',}">
         <!-- <animated-rotate></animated-rotate> -->
     <!-- <animated-flip></animated-flip> -->

    <view :style="{flexDirection:'row',justifyContent:'space-evenly',alignItems:'flex-end',height:300}">
     <animatedView
        :style="{
          marginBottom: movingMargin,
          height: 50,
          width: 50,
          borderRadius:50,
          backgroundColor: 'red',
          }"
          />
          <animatedView
        :style="{
          marginBottom: movingMargin1,
          height: 50,
          width: 50,
          borderRadius:50,
          backgroundColor: 'green',
          }"
          />
          <animatedView
        :style="{
          marginBottom: movingMargin2,
          height: 50,
          width: 50,
          borderRadius:50,
          backgroundColor: 'blue',

          }"
          />
          <animatedView
        :style="{
          marginBottom: movingMargin3,
          height: 50,
          width: 50,
          borderRadius:50,
          backgroundColor: 'purple',

          }"
          />
          </view>
          <!-- </view>  -->
    </view>
</template>
```

Jumping right into our animation function ,

```js
animate: function() {
      this.animatedValue.setValue(0);
      this.animatedValue1.setValue(0);
      this.animatedValue2.setValue(0);
      this.animatedValue3.setValue(0);

      animated
        .stagger(100, [
          animated.timing(this.animatedValue3, {
            toValue: 2,
            duration: this.duration,
            easing: Easing.linear
          }),

          // animated.delay(700),
          animated.timing(this.animatedValue2, {
            toValue: 2,
            duration: this.duration,
            easing: Easing.linear
          }),

          // animated.delay(700),
          animated.timing(this.animatedValue1, {
            toValue: 2,
            duration: this.duration,
            easing: Easing.linear
          }),
          animated.timing(this.animatedValue, {
            toValue: 2,
            duration: this.duration,
            easing: Easing.linear
          })
          // animated.delay(700),
        ])
        .start(event => {
          // console.log(event);
          if (event.finished) {
            this.animate();
          }
          // this.animate();
        });
      this.movingMargin = this.animatedValue.interpolate({
        inputRange: [0, 0.5, 1, 1.5, 2],
        outputRange: [0, 200, 0, 200, 0]
      });
      this.movingMargin1 = this.animatedValue1.interpolate({
        inputRange: [0, 0.5, 1, 1.5, 2],
        outputRange: [0, 200, 0, 200, 0]
      });
      this.movingMargin2 = this.animatedValue2.interpolate({
        inputRange: [0, 0.5, 1, 1.5, 2],
        outputRange: [0, 200, 0, 200, 0]
      });
      this.movingMargin3 = this.animatedValue3.interpolate({
        inputRange: [0, 0.5, 1, 1.5, 2],
        outputRange: [0, 200, 0, 200, 0]
      });
    }
```

The `animated.stagger` executes each of the animation types provided to it at a delay of `100`. Meaning the second animation is started after 100 ms after the first one starts and so on.

### Stagger Animation

This is simialr to the previous animation but here we combine the staggered animation from the previous step with a `spring` animation at the end of it. The animated properties include `marginLeft` and `left`.

The below snippet shows just one component using these animated properties :

```html
<animatedView
        :style="{
          marginLeft: movingMargin,
          left: springValue,
          height: 50,
          width: 50,
          borderRadius:50,
          backgroundColor: 'purple',
          }"
          />
```

The animation function is here similar to the previous one. But in this, we have combined each of the stagger animations with a spring animation within a sequence.

```js
animate: function() {
      this.animatedValue.setValue(0);
      this.animatedValue1.setValue(0);
      this.animatedValue2.setValue(0);
      this.animatedValue3.setValue(0);
      this.springValue.setValue(20);
      this.springValue1.setValue(20);
      this.springValue2.setValue(20);
      this.springValue3.setValue(20);

          animated.stagger(100, [
            // animated.delay(700),

            // animated.delay(700),
            animated.sequence([
              animated.timing(this.animatedValue, {
                toValue: 2,
                duration: this.duration,
                easing: Easing.linear
              }),
              animated.spring(this.springValue, {
                toValue: 0,
                friction: 1
              })
            ]),

            animated.sequence([
              animated.timing(this.animatedValue1, {
                toValue: 2,
                duration: this.duration,
                easing: Easing.linear
              }),
              animated.spring(this.springValue1, {
                toValue: 0,
                friction: 1
              })
            ]),
            animated.sequence([
              animated.timing(this.animatedValue2, {
                toValue: 2,
                duration: this.duration,
                easing: Easing.linear
              }),
              animated.spring(this.springValue2, {
                toValue: 0,
                friction: 1
              })
            ]),
            animated.sequence([
              animated.timing(this.animatedValue3, {
                toValue: 2,
                duration: this.duration,
                easing: Easing.linear
              }),
              animated.spring(this.springValue3, {
                toValue: 0,
                friction: 1
              })
            ])

            // animated.delay(700),
          ])
        .start(() => {
          this.animate();
        });

      this.movingMargin = this.animatedValue.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 100]
      });
      this.movingMargin1 = this.animatedValue1.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 100]
      });
      this.movingMargin2 = this.animatedValue2.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 100]
      });
      this.movingMargin3 = this.animatedValue3.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 100]
      });
    }
```

The above produces this which is just a treat to the eyes.

[gif]
