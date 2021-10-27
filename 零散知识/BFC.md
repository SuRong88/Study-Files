# [浅谈什么是前端BFC](https://www.cnblogs.com/7Ezreal/p/12916481.html)

### 简介：什么是BFC？

BFC(Block Formatting Context)的英文缩写简称，block可以理解为一个简单的盒模型， Formatting Context则为block的上下文渲染环境。其作用是使内部元素的布局不受外部元素影响。

#### BFC的触发条件：

- 根元素,也就是html根标签；
- position：fixed/absoluted;
- float属性值不是none的；
- overflow属性值不是visible的；
- display属性值：inline-display/table-cell/table-caption/flex/inline-flex;

#### BFC作用：

- bfc内部元素的布局不受外部元素影响。
- bfc区域不会出现margin重叠
- bfc区域计算高度时候会自动计算浮动元素。
- bfc区域不会和浮动元素重合。

#### BFC的场景应用：

1. 防止margin出现重叠：
   这里可以很明显的看到div1的margin是生效了的，但是div2的margin在于1相邻的部分是没有起到作用的
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518170922233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTE3Mjgz,size_16,color_FFFFFF,t_70)
   在这里我们在div2外部套上一个div，并添加overflow：hidden属性，使其成为一个bfc区域，这样div2的margin也就生效了。
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518170809563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTE3Mjgz,size_16,color_FFFFFF,t_70)
2. 两栏布局，防止出现文字环绕效果：
   在这里可以看到当div1设置为浮动后，会浮动在div2的上面，看起来像是div1被div2环绕包裹起来的感觉；
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518170606697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTE3Mjgz,size_16,color_FFFFFF,t_70)
   为div2添加一个包裹div并设置overflow属性，使其成为一个BFC区域，这样就可以避免出现文字环绕效果，并且可以实现自动实现两栏布局效果。![在这里插入图片描述](https://img-blog.csdnimg.cn/20200518170722167.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTE3Mjgz,size_16,color_FFFFFF,t_70)
3. 清除浮动，防止元素塌陷：
   这个很好理解就是我们在页面布局的时候，如果不给父元素设置一个高度的话，子元素设置浮动后，子元素并不能将父元素的高度自动撑起来，这个时候给父元素设置一个overflow：hidden属性后，就可以起到清除浮动的作用。