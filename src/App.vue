<template>
  <div id="app">
    <table>
      <thead v-pre>
        <tr>
          <th class="body">内容</th>
          <th class="delete"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="memo in memos"
            v-bind:key="memo.id">
          <td v-bind:class='generateClassName(memo)' v-on:click="selectMemo(memo)">{{ splitTitle(memo) }}</td>
          <td class="delete">
            <button class="delete" v-on:click="removeMemo(memo)" v-bind:disabled="editMode">
              削除
            </button>
          </td>
        </tr>
        <tr v-bind:key="-1">
          <td class="new">
            <button class="new" v-on:click="selectMemo({})">
              新規
            </button>
          </td>
          <td></td>
        </tr>
      </tbody>
    </table>

    <EditMemo v-show="editMode" v-bind:selectedMemo="this.selectedMemo" v-on:saveMemo="saveMemo" v-on:unselectMemo="unselectMemo"/>
  </div>
</template>

<script>
const STORAGE_KEY = 'memos-app'
const memoStorage = {
  fetch () {
    const memos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    memos.forEach(function(memo, index) {
      memo.id = index
    })
    memoStorage.uid = memos.length
    return memos
  },
  save (memos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(memos))
  }
}

import EditMemo from './components/EditMemo.vue'

export default {
  name: 'App',
  components: {
    EditMemo
  },
  data () {
    return {
      memos: memoStorage.fetch(),
      editMode: false,
      selectedMemo: {}
    }
  },

  methods: {
    selectMemo (memo) {
      this.selectedMemo = memo
      this.editMode = true
    },

    removeMemo (memo) {
      const index = this.memos.indexOf(memo)
      this.memos.splice(index, 1)
      memoStorage.save(this.memos)
    },

    saveMemo (memo) {
      console.log(memo.id)
      const body = memo.body && memo.body.trim()
      if (!body) {
        return
      }
      if(memo.id === undefined) {
        this.memos.push({
          id: memoStorage.uid++,
          body: body
        })
      } else {
        const index = this.memos.indexOf(memo)
        this.memos[index].body = memo.body
      }
      memoStorage.save(this.memos)
      this.unselectMemo()
    },

    unselectMemo () {
      this.selectedMemo = {}
      this.memos = memoStorage.fetch()
      this.editMode = false
    },

    generateClassName (memo) {
      const enabled = !this.editMode && this.selectedMemo.id === undefined || memo.id === this.selectedMemo.id
      return `body${enabled ? '' : '-disabled'}`
    },

    splitTitle (memo) {
      return memo.body.split('\n')[0]
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}
#app {
  max-width: 640px;
  margin: 0 auto;
}
table {
  width: 100%;
  border-collapse: collapse;
}
thead th {
  border-bottom: 2px solid #0099e4;
  color: #0099e4;
}
th {
  padding: 0 8px;
  line-height: 40px;
}
thead th.delete {
  width: 60px;
}
tbody td.delete {
  text-align: center;
}
tbody td.body-disabled {
  color: lightgray;
}
tbody tr td,
tbody tr th {
  border-bottom: 1px solid #ccc;
  transition: all 0.4s;
}
tbody tr.done td,
tbody tr.done th {
  background: #f8f8f8;
}
tbody tr:hover td,
tbody tr:hover th {
  background: #e8f6ff;
}
input.new {
  width: 70%;
}
button {
  border: none;
  border-radius: 5px;
  line-height: 30px;
  margin: 0 5px;
  padding: 0 10px;
  color: #fff;
  cursor: pointer;
}
button.edit:enabled, button.new:enabled {
  background: #0099e4;
}
button.delete:enabled {
  background: tomato;
}
button.delete:disabled, button.edit:disabled, button.new:disabled {
  background: gray;
}
textarea {
  height: 15em;
  width: 100%;
}
</style>
