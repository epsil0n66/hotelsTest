<template>
  <button class="btn" onclick="modal.showModal()">
    Показать условия тарифа
  </button>
  <dialog id="modal" class="modal">
    <div class="modal-box">
      <button
        v-for="segment in segments"
        :key="segment.id"
        class="btn m-2"
        @click="currentSegment = segment"
      >
        {{ segment.Arrival.IATA_LocationCode }} —
        {{ segment.Dep.IATA_LocationCode }}
      </button>
      <div v-if="currentRuleText">
        <button
          v-for="chapter in currentRuleChapters"
          :key="chapter.id"
          class="btn btn-xs m-1"
          @click="currentRuleChapterText = chapter"
        >
          Глава {{ currentRuleChapters.indexOf(chapter) + 1 }}
        </button>
        <div v-if="currentRuleChapterText">
          <div v-html="currentRuleChapterText" />
        </div>
      </div>
      <div class="modal-action">
        <form method="dialog">
          <button class="btn">Закрыть</button>
        </form>
      </div>
    </div>
  </dialog>
</template>

<script setup>
  import data from './assets/rules.json'
  // Данные json
  const segments = reactive(data.DataLists.PaxSegmentList.PaxSegment)
  const rules = reactive(data.Rules)
  // Пользователь выбирает сегмент (маршрут) по клику, мы получаем по нему данные
  const currentSegment = ref()
  const currentRuleText = computed(() => {
    if (currentSegment.value) {
      return rules.find(
        (el) => el.PaxSegmentRefID === currentSegment.value.PaxSegmentID
      )
    }
  })
  // После выбора сегмента разбиваем правила на главы используя регулярные выражения
  // В тексте некоторые главы отсутствуют, поэтому тут они разбиваются на главы в порядке нахождения
  const currentRuleChapters = computed(() => {
    if (currentRuleText.value) {
      let chapters =
        currentRuleText.value.FareRuleText.Remark.RemarkText.split(
          /b\>\d{1,2}[\.]{1}/
        )
      chapters.shift()
      return chapters
    }
  })
  const currentRuleChapterText = ref()
</script>
