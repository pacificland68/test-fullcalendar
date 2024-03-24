
<template>
  <div class="full">
    <FullCalendar :options="calendarOptions" ref="calendarRef" @eventsSet="onEventsSet"/>
  </div>
</template>

<script setup lang="ts">
import FullCalendar from '@fullcalendar/vue3'
import timeGridPlugin from '@fullcalendar/timegrid';
import interactionPlugin from '@fullcalendar/interaction';
import {ref, onMounted, nextTick} from 'vue'

const calendarOptions = ref({
  timeZone: 'UTC',
  plugins: [timeGridPlugin, interactionPlugin],
  height: '800px',
  initialView: 'timeGridWeek',
  slotLabelInterval: '01:00:00', // 时间轴每小时显示一次
  slotDuration: '00:10:00',
  selectable: true,
  slotLabelContent: function(arg:any) {
      // 每小时显示一次标签，格式化为你喜欢的样式
      return {html: `<div style='text-align:center;'>${arg.date.getHours()}:00</div>`};
  },
  resources: [
    { id: 'a', title: 'Room A' },
    { id: 'b', title: 'Room B' },
    { id: 'c', title: 'Room C' },
    { id: 'd', title: 'Room D' }
  ],
  events: 'https://fullcalendar.io/api/demo-feeds/events.json?with-resources=4&single-day'
    })
let calendarRef = ref(null)

onMounted(() => {
  adjustTimeSlotHeight();
})

const adjustTimeSlotHeight = () => {
      // 使用ref获取FullCalendar DOM节点
      const calendarEl = calendarRef.value?.$el;

      if (calendarEl) {
        console.log('calendarEl')
        // 动态调整时间槽高度
        calendarEl.querySelectorAll('.fc-timegrid-slot').forEach(slot => {
          console.log('slot', slot)
          slot.style.height = '10px'; // 自定义高度
        });

        // 调整时间标签的行高来确保对齐
        calendarEl.querySelectorAll('.fc-timegrid-slot-label').forEach(label => {
          label.style.lineHeight = '10px'; // 确保与时间槽高度一致
        });
      }
    };

    const onEventsSet = () => {
      nextTick(() => {
        adjustTimeSlotHeight();
      });
    };
</script>


<style lang="less" scoped>
.full{
  :deep(.fc .fc-timegrid-slot){
    height: 20px !important;
  }
  :deep(.fc .fc-timegrid-slot-label){
    line-height: 20px !important; 
  }
  :deep(.fc .fc-timegrid-slot-label:not(:nth-child(6n+1))){
    display: none !important;
  }
  :deep(.fc .fc-timegrid-slot-minor){
    border-top: 1px solid white !important;
  }
  :deep(.fc .fc-timegrid-slot-lane){
  }
  :deep(.fc .fc-scrollgrid-shrink){
    border-top: 1px solid black !important;
  }
  :deep(.fc .fc-timegrid-slot-label:nth-child(6n+1)){
    height: 1px !important; /* 这个值取决于你希望的单元格高度，可能需要调整 */
    line-height: 1px !important;
  }
  :deep(.fc .fc-timegrid-slot){
    border-bottom: none !important;
  }
  :deep(.fc .fc-timegrid-slot:last-child){
    border-bottom: 1px solid #ddd !important;
  }
}

.fc-timegrid-slot {
    height: 50px; /* 设置为你希望的高度 */
}

/* 如果你还想调整时间标签（即左侧时间列）的行高，以使其与时间槽的高度相匹配 */
.fc-timegrid-slot-label {
    line-height: 50px; /* 保持这个值与上面的时间槽高度一致 */
}
</style>
