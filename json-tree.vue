<template>
  <div class="tree-container">
    <ul class="tree">
      <li class="start">
        <a href="javascript:;" class="toggle" @click="toggleCollapse">{{collapse ? '+': '-'}}</a>
        <span v-if="pkey">
          <span class="tree-item-key">
            "{{pkey}}"
          </span>:
        </span>
        <span v-if="isArr">[</span>
        <span v-else>{</span>
        <template v-if="collapse">
          <span>...</span>
          <span v-if="isArr">]</span>
          <span v-else>}</span>
          <span class="hint">{{entries.length}}项</span>
        </template>
      </li>
      <li class="main" v-show="!collapse" v-for="[key, value] in entries" :key="key">
        <JsonTree
          v-if="typeof value === 'object'"
          :data="value" :pkey="key"
          :is-end="lastKey===key"
        />
        <span v-else>
          <span class="tree-item-key">
            "{{key}}"
          </span>:
          <span class="tree-item-value" v-if="typeof value === 'string'">"{{value}}"</span>
          <span class="tree-item-value number" v-else>{{value}}</span>
          <span v-if="lastKey !== key">,</span>
        </span>
      </li>
      <li class="end" v-show="!collapse">
        <span v-if="isArr">]</span>
        <span v-else>}</span>
        <span v-if="!isEnd">,</span>
      </li>
    </ul>
  </div>
</template>
<script lang="ts">
import {Vue, Component, Prop, Emit} from 'vue-property-decorator';

@Component
export default class JsonTree extends Vue {
  @Prop({default: {}, type: [Object, Array]}) private data!: object;
  @Prop({default: '', type: String}) private pkey!: string;
  @Prop({default: true, type: Boolean}) private isEnd!: boolean;
  @Prop({default: false, type: Boolean}) private initialCollapse!: boolean;

  private collapse = this.initialCollapse;

  get entries() {
    return Object.entries(this.data);
  }

  get lastKey() {
    return Object.keys(this.data).pop();
  }

  get isArr() {
    return Array.isArray(this.data);
  }

  @Emit() private toggleCollapse() {
    this.collapse = !this.collapse;
  }
}
</script>
<style lang="less" scoped>
  .tree-container {
    padding-left: 30px;
    font-size: 14px;
    line-height: 1.5;
    .tree-container {
      padding-left: 0;
    }
    .tree {
      text-align: left;
      font-family: monospace, 'PT Mono';
      position: relative;
      .toggle {
        position: absolute;
        left: -11px;
        text-decoration: none;
      }
      .hint {
        opacity: .4;
        margin-left: 4px;
      }
      .main {
        padding-left: 2em;
        border-left: 1px dotted #ccc;
      }
      .tree-item-key {
        color: crimson;
        // padding-left: 10px;
      }
      .tree-item-value {
        color: blue;
        &.number{
          color:rgb(0, 174, 255)
        }
      }
    }
  }
  ul {
    list-style: none;
    margin: 0;
    padding-left: 0;
  }
</style>

