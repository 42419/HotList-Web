<template>
  <section class="www_vvhan_com">
    <header>
      <div class="main">
        <div class="logo">
          <img src="https://tc.liyunfei.eu.org/v2/XoLKBfT.png">
          <span>今日热榜</span>
        </div>
      </div>
    </header>
    <main>
      <header>
        <Alert>
          <DrawingPinIcon />
        </Alert>
      </header>

      <section class="hotlist">
        <ListItem v-for="i in hotlistKey" :key="i.name" :item="i" @refreshFn="refreshFn" />
      </section>
    </main>
    <footer>
      <p><img src="./assets/svg/ing.svg"></p>
      <p>
        <a href="https://pages.cloudflare.com" target="_blank" rel="noopener noreferrer"><img
            src="./assets/svg/framework.svg"></a>
        <a href="https://www.cloudflare.com/zh-cn/application-services/products/cdn/" target="_blank"
          rel="noopener noreferrer"><img src="./assets/svg/cdn.svg"></a>
        <a href="https://vuejs.org" target="_blank" rel="noopener noreferrer"><img src="./assets/svg/web.svg"></a>
        <a href="https://api.vvhan.com" target="_blank"><img src="./assets/svg/surppot.svg"></a>
      </p>
    </footer>
  </section>
  <Toaster />
</template>


<script setup lang="ts">
import { ref } from 'vue'
import moment from 'moment'
import { DrawingPinIcon } from '@radix-icons/vue'
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert'
import ListItem from '@/components/ListItem/ListItem.vue'
import { Toaster } from '@/components/ui/toast'
import { useToast } from '@/components/ui/toast/use-toast'
const { toast } = useToast();
// 热榜列表
const hotlistKey = ref<any[]>([
  { key: 'wbHot', name: "微博", sub: "热搜榜", data: [] },
  { key: 'toutiao', name: "今日头条", sub: "热点", data: [] },
  { key: 'zhihuHot', name: "知乎热榜", sub: "热度", data: [] },
  { key: 'zhihuDay', name: "知乎日报", sub: "", data: [] },
  { key: 'bili', name: "哔哩哔哩", sub: "全站日榜", data: [] },
  { key: 'baiduRD', name: "百度热点", sub: "指数", data: [] },
  { key: 'douyinHot', name: "抖音", sub: "热点榜", data: [] },
  { key: 'douban', name: "豆瓣小组", sub: "讨论精选", data: [] },
  { key: 'itNews', name: "IT之家", sub: "最新资讯", data: [] },
])

// 初始化数据请求
const vhInit = async () => {
  try {
    const res = await fetch("https://api.vvhan.com/api/hotlist/all")
    await new Promise((r) => setTimeout(r, 666))
    toast({ title: 'Init', description: '热榜获取成功', });
    const { data } = await res.json()
    hotlistKey.value.forEach((i: any) => {
      const currentItem = data.find((item: any) => item.name == i.name)
      if (currentItem) {
        i.data = currentItem.data;
        i.updateStr = formatTime(currentItem.update_time);
      }
    })
  } catch (error) {
    toast({ description: '今日热榜 获取失败', variant: 'destructive' });
  }
}


// 刷新数据
const updateStatus = ref<boolean>(false)
const refreshFn = async (item: any) => {
  if (updateStatus.value) return;
  updateStatus.value = true;
  item.data = [];
  const res = await fetch(`https://api.vvhan.com/api/hotlist/${item.key}`);
  const data = await res.json()
  await new Promise((r) => setTimeout(r, 666))
  item.data = data.data;
  item.updateStr = formatTime(data.update_time);
  toast({ title: 'Update', description: `${item.name} 更新成功`, });
  updateStatus.value = false;
}
vhInit()

// 时间处理
const formatTime = (time: string) => {
  const targetDateTime = moment(time);
  const now = moment();
  const duration = moment.duration(now.diff(targetDateTime));
  return `${duration.hours()}小时${duration.minutes()}分钟前`
}

</script>


<style scoped>
@import '@/assets/index.less';
</style>
