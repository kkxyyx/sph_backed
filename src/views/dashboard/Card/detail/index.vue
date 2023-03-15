<template>
  <div>
    <div class="card-head">
      <span>{{ title }}</span>
      <span>图标</span>
    </div>
    <div class="card-content">
      {{ count }}
    </div>
    <div class="card-charts">
      <slot name="charts" />
    </div>
    <div class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>

<script>

export default {
  name: 'Detail',
  // eslint-disable-next-line vue/require-prop-types
  props: ['title', 'count']

}
</script>

<style>
.card-head{
    display: flex;
    justify-content: space-between;
}
.card-content{
    font-size: 30px;
    padding: 10px 0;
}
.card-charts{
    height: 50px;
    font-size: 15px;
    padding:10px 0
}
.card-footer{
    font-size: 15px;
    border-top:1px solid #eee ;
    padding-top: 10px;
}
</style>
