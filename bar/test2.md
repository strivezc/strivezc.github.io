# uniapp+vue3 用css写一个文字跑马灯组件


## 前言
最近微信小程序项目需要一个文字跑马灯的效果，写了一个组件记录下来。

### 1、主要是用到了css3的animation，先用@keyframes定义一个动画，表示从元素的最左边移动到最右边
```css
  @keyframes move-data {
    0% {
      transform: translateX(100%);
    }
    to {
      transform: translateX(-100%);
    }
  }
``` 

### 2、然后给盒子定义宽度，超出隐藏；文字部分使用animation，动画名称就用上面的move-data，animation-duration动画时长默认10s 后面会根据内容长度去计算动画时长，infinite无限动画
```css
 .move-wrapper {
    position: relative;
    display: flex;
    flex-grow: 1;
    overflow: hidden;
    width: 421rpx;
    height: 80rpx;
    background: rgba(0, 0, 0, 0.6);
    border-radius: 16rpx;
    .move-wrap {
      display: flex;
      align-items: center;
      justify-content: center;
      animation: move-data 10s linear infinite;
      white-space: nowrap;
      width: max-content;
      font-size: 28rpx;
      color: #ffffff;
      line-height: 36rpx;
    }
  }  
``` 

### 3、template模板
```html
<template>
  <view class="move-wrapper">
    <view class="move-wrap" :style="{ 'animation-duration': duration + 's' }">{{props.moveText}}</view>
  </view>
</template>
``` 

### 4、js部分，使用了vue3的hooks，getCurrentInstance()表示组件实例，相当于vue2 this
```js
<script setup>
  import useMoveText from '../../extends/useMoveText';
  const props = defineProps({
    moveText: {
      type: String,
      default: '这里显示公共，滚动效果；这里显示公共，滚动效果；',
    },
  });
  const { duration } = useMoveText('.move-wrap', 421, getCurrentInstance());
</script>
``` 

### speed表示每秒滚动的宽度，获取文字内容的总长度，动画时长 duration=文字总长度/每秒滚动宽度，得出总共需要滚动的时长
```js
// useMoveText.js
/**
 *@className css类名
 *@boxWidth 滚动盒子默认宽度
 *@that 组件实例
 */
export default function useMoveText(className, boxWidth, that) {
  const speed = 30; // 每秒滚动长度
  const duration = ref(10);

  onMounted(() => {
    let element = uni.createSelectorQuery().in(that).select(className);
    element
      .boundingClientRect(function (data) {
        // 如果文字长度小于盒子的宽度，就取盒子宽 不然得出的时长会小，滚动速度就会变快
        const width = data.width > boxWidth ? data.width : boxWidth;
        duration.value = Math.round(width / speed);
      })
      .exec(function (res) {});
  });

  return {
    duration
  };
}
```

### 最终实现的效果图
![Image text](https://cdn.jsdelivr.net/gh/strivezc/common/select1.gif)


