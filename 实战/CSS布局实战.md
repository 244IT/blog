### 优惠券

.wxml

```
<view class="cart-item">
  <view class="cart-left">
    <view>
      <text>￥</text>
      <text class="cart-price">{{item.coupon_amount}}</text>
    </view>
    <view class="cart-limit">满{{item.transaction_minimum}}元可用</view>
  </view>
  <view class="cart-center">
    <text>{{item.stock_name}}</text>
    <text class="cart-time">{{filter.filterTime(item.available_end_time)}}之前有效</text>
  </view>
  <view class="cart-right"  data-info="{{item}}" data-index="{{index}}" bindtap="onGetCart">
    <button class="cart-btn {{filter.filterStockStatus(item)}} {{item.empty ? 'grey' : ''}}" hover-class="hover-cart-btn">领取</button>
  </view>
  <!-- 分割线 -->
  <view class="line-wrap">
    <view class="top-round"></view>
    <view class="line"></view>
    <view class="bottom-round"></view>
  </view>
</view>
```

.wxss

```
.cart-item{
  position: relative;
  display: flex;
  padding: 40rpx 24rpx;
  margin-bottom: 24rpx;
  border: 1rpx solid #FFDAD7;
  border-radius: 10rpx;
  background-color: #FFF;
  box-sizing: border-box;
}
/* 优惠券中 */
.cart-center{
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  margin-left: 64rpx;
}
.cart-time{
  font-weight: 500;
  font-size: 22rpx;
  color: #999;
}
/* 优惠卷右 */
.cart-right{
  display: flex;
  justify-content: center;
  align-items: center;
}
.cart-btn{
  width: 114rpx!important;
  height: 48rpx!important;
  padding: 0!important;
  line-height: 48rpx!important;
  border-radius: 24rpx!important;
  font-weight: 500!important;
  font-size: 24rpx!important;
  color: #FFF;
  text-align: center;
  background-color: #FB493E;
}
/* 没有可领取的优惠券 */
.empty{
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 100rpx 0 140rpx;
}

.empty-img{
  width: 300rpx;
  height: 188rpx;
}

.empty text{
  font-size: 28rpx;
  color: #999;
}

/* 分割线 */
.line-wrap{
  position: absolute;
  right: 200rpx;
  top: -1rpx;
  bottom: 0rpx;
}
.top-round, .bottom-round{
  position: absolute;
  width: 40rpx;
  height: 20rpx;
  border-radius: 0 0 40rpx 40rpx;
  border: 1rpx solid #FFDAD7;
  border-top: none;
  background-color: #F6F5F7;
  z-index: 1;
}
.bottom-round{
  bottom: -1rpx;
  border-top: 1rpx solid #FFDAD7;
  border-bottom: none;
  border-radius: 40rpx 40rpx 0 0;
}
.line{
  position: absolute;
  left: 19rpx;
  width: 1rpx;
  top: 0;
  bottom: 0;
  border-left: 1rpx dotted #FFDAD7;
}
```

