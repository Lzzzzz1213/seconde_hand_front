<template>
    <div class="foot-container">
        <div class="author">基于SpringBoot的二手交易系统，作者：毛奕铭------完成时间2023-3-15</div>
    </div>
</template>

<script>
    export default {
        name: "Foot"
    }
</script>

<style scoped>
    .foot-container {
        width: 100%;
        display: flex;
        justify-content: center;
        height: 40px;
        padding-top: 10px;
        position: relative;
        text-align: center;
    }

    .author {
        color: #999999;
        font-size: 14px;

    }
    .finishtime{
      color: #999999;
      font-size: 14px;
      position: absolute;
    }
</style>
