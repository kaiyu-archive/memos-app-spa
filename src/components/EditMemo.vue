<template>
  <div class="edit">
    内容 <textarea class="new" v-model="selectedMemo.body"></textarea>
    <button class="edit" v-on:click="saveMemo(selectedMemo)">
      編集
    </button>
    <button class="edit" v-on:click="unselectMemo()">
      キャンセル
    </button>
  </div>
</template>

<script>
export default {
  name: 'EditMemo',
  props: [ 'selectedMemo' ],

  methods: {
    saveMemo: function(memo) {
      this.$emit('saveMemo', memo)
    },
    unselectMemo: function() {
      this.$emit('unselectMemo')
    }
  }
}
</script>

<style>
</style>
