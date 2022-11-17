<template>
  <fieldset>
    <legend>
      Style Controls
      <button @click="showStyleControls = !showStyleControls">
        {{ showStyleControls ? "-" : "+" }}
      </button>
    </legend>
    <div class="controls" v-if="showStyleControls">
      <div class="input-wrapper">
        <label for="accent" class="label">Accent Color: </label>
        <input id="accent" type="text" class="input-el" v-model="accentColor" />
      </div>
      <div class="input-wrapper">
        <label for="accent" class="label">Accent Complementary Color: </label>
        <input
          id="accent"
          type="text"
          class="input-el"
          v-model="complementaryColor"
        />
      </div>
      <div class="input-wrapper">
        <label for="accent" class="label">Default Text Color: </label>
        <input id="accent" type="text" class="input-el" v-model="textColor" />
      </div>
      <div class="input-wrapper">
        <label for="accent" class="label">Font Size: </label>
        <input id="accent" type="text" class="input-el" v-model="fontSize" />
      </div>
      <div class="input-wrapper">
        <label for="accent" class="label">Border Color: </label>
        <input id="accent" type="text" class="input-el" v-model="borderColor" />
      </div>
      <div class="input-wrapper">
        <label for="accent" class="label">Border Width: </label>
        <input id="accent" type="text" class="input-el" v-model="borderWidth" />
      </div>
      <button class="button" @click="addColumn">Add Column</button>
      <button class="button" @click="addRow">Add Row</button>
      <button class="button" @click="clearRows">Clear Rows</button>
    </div>
  </fieldset>
  <fieldset>
    <legend>
      Selected Text Controls
      <button @click="showTextControls = !showTextControls">
        {{ showTextControls ? "-" : "+" }}
      </button>
    </legend>
    <div class="controls" v-if="showTextControls">
      <button class="button" @click="makeBold">Make It Bold</button>
      <div>
        <strong>Selected Text:</strong>
        <p>{{ text }}</p>
      </div>
    </div>
  </fieldset>
  <br />
  <div class="table-container">
    <div class="table">
      <div
        spellcheck="false"
        :class="[
          'cell',
          'column',
          i + 1 === columns.length && 'no-border',
          i + 1 === columns.length && 'top-right-radius'
        ]"
        :key="i"
        v-for="(col, i) in columns"
      >
        <span contenteditable>{{ col.label }}</span>
        <button class="remove-button" @click="removeColumn(i)">Remove</button>
      </div>
      <div
        spellcheck="false"
        :class="[
          'cell',
          'data',
          row % columns.length === 0 && 'no-border',
          row === columns.length * (rowCount - 1) + 1 && 'bottom-left-radius',
          row <= columns.length * (rowCount - 1) && 'border-bottom'
        ]"
        :key="i"
        v-for="(row, i) in columns.length * rowCount"
      >
        <span contenteditable>Column Data {{ row }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useTextSelection } from "@vueuse/core";

interface Column {
  label: string;
}

const showStyleControls = ref(true);
const showTextControls = ref(true);

const { text } = useTextSelection();

function makeBold() {
  document.execCommand("bold", false, undefined);
}

const columns = ref<Column[]>([
  {
    label: "Column 1"
  },
  {
    label: "Column 2"
  }
]);

const rowCount = ref(1);

function addColumn() {
  columns.value.push({
    label: "New Column"
  });
}

function removeColumn(index: number) {
  columns.value.splice(index, 1);
}

function addRow() {
  rowCount.value += 1;
}

function clearRows() {
  rowCount.value = 1;
}

const accentColor = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue(
      "--accent-color"
    ),
  set: val => document.documentElement.style.setProperty("--accent-color", val)
});
const complementaryColor = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue(
      "--complementary-text"
    ),
  set: val =>
    document.documentElement.style.setProperty("--complementary-text", val)
});
const textColor = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue(
      "--default-text"
    ),
  set: val => document.documentElement.style.setProperty("--default-text", val)
});
const fontSize = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue("--font-size"),
  set: val => document.documentElement.style.setProperty("--font-size", val)
});
const borderColor = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue(
      "--border-color"
    ),
  set: val => document.documentElement.style.setProperty("--border-color", val)
});
const borderWidth = computed({
  get: () =>
    getComputedStyle(document.documentElement).getPropertyValue(
      "--border-width"
    ),
  set: val => document.documentElement.style.setProperty("--border-width", val)
});
</script>

<style lang="scss">
@use "./assets/fistik.font";

:root {
  --accent-color: #ffcc00;
  --complementary-text: #222;
  --default-text: #222;
  --font-size: 25px;
  --border-color: #000;
  --border-width: 2px;
}

* {
  box-sizing: border-box;
  font-family: Fistik, sans-serif;
  font-size: var(--font-size, 16px);
}

html,
body {
  margin: 0;
  color: var(--default-text, #000);
  font-size: var(--font-size, 16px);
  font-family: Fistik, sans-serif;
}

fieldset {
  margin: 10px 20px;
  border-radius: 6px;
  legend {
    display: flex;
    gap: 10px;
    font-size: 24px;
    button {
      display: flex;
      align-items: center;
      justify-content: center;
      border: 1px solid var(--complementary-text);
      border-radius: 999px;
      background-color: var(--accent-color);
      color: var(--complementary-text);
      line-height: 50%;
      width: 28px;
      height: 28px;
    }
  }
  .controls {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    gap: 20px;
    .input-wrapper {
      display: flex;
      flex-direction: column;
      .label {
        font-weight: 600;
        font-size: 18px;
      }
      .input-el {
        padding: 6px 12px;
        border: 1px solid #000;
        border-radius: 6px;
        font-size: 18px;
      }
    }
    .button {
      padding: 14px 20px;
      border: 1px solid var(--complementary-text);
      border-radius: 6px;
      background-color: var(--accent-color);
      color: var(--complementary-text);
      font-size: 18px;
      height: 100%;
    }
  }
}

.table-container {
  --border-radius: 10px;
  padding: 0px 20px;
  width: 100%;
  .table {
    display: grid;
    gap: 0;
    grid-template-columns: repeat(v-bind("columns.length"), 1fr);
    border: var(--border-width) solid var(--border-color);
    border-radius: var(--border-radius);
    .cell {
      padding: 10px 20px;
      font-size: var(--font-size);
      --inner-border: calc(var(--border-radius) - var(--border-width));
      &:not(.no-border) {
        border-right: var(--border-width) solid var(--border-color);
      }
      &:first-child {
        border-radius: var(--inner-border) 0 0 0;
      }
      &:last-child {
        border-radius: 0 0 var(--inner-border) 0;
      }
      &.top-right-radius {
        border-radius: 0 var(--inner-border) 0 0;
      }
      &.bottom-left-radius {
        border-radius: 0 0 0 var(--inner-border);
      }
      &.column {
        display: flex;
        align-items: center;
        justify-content: center;
        position: relative;
        border-bottom: var(--border-width) solid var(--border-color);
        background-color: var(--accent-color);
        color: var(--complementary-text);
        font-weight: 900;
        span {
          text-align: center;
          vertical-align: middle;
        }
        &:hover {
          .remove-button {
            opacity: 1;
            pointer-events: all;
          }
        }
        .remove-button {
          position: absolute;
          top: 10px;
          right: 10px;
          border: none;
          border-radius: var(--border-radius);
          background-color: var(--complementary-text);
          color: var(--accent-color);
          opacity: 0;
          pointer-events: none;
          cursor: pointer;
          user-select: none;
        }
      }
      &.data {
        display: flex;
        align-items: center;
        &.border-bottom {
          border-bottom: var(--border-width) solid var(--border-color);
        }
        background-color: white;
        color: var(--default-text);
      }
    }
  }
}
</style>
