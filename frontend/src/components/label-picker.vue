<template>
  <section class="label-picker">
    <header>
      <button class="close-btn" v-if="!isAddTitle" @click="$emit('close-labels')">
        <font-awesome-icon icon="times" />
      </button>
      <button class="close-btn" v-else @click="toggleAddTitle()">
        <font-awesome-icon icon="times" />
      </button>
      <h2>Labels</h2>
    </header>

    <main>
      <section v-if="!isAddTitle">
        <div
          class="label-content"
          v-for="labelColor in Object.keys(allLabelsMap)"
          :key="labelColor"
        >
          <span
            class="label"
            :style="{backgroundColor:labelColor}"
            @click="updateLabels(labelColor)"
          >
            <span class="label-title" v-if="allLabelsMap[labelColor]">{{allLabelsMap[labelColor]}}</span>
            <span class="check-title el-icon-check" v-if="isSelectedLabel(labelColor)"></span>
          </span>
          <button
            class="add-title-btn el-icon-edit"
            title="Add title"
            @click="toggleAddTitle(labelColor)"
          ></button>
        </div>
      </section>

      <form class="title-form" v-else @submit.prevent="saveTitle">
        <div class="selected-color el-icon-check" :style="{backgroundColor:currLabelColor}"></div>
        <div class="form-header">Add a title</div>
        <input
          class="task"
          type="text"
          placeholder="Enter title..."
          maxlength="10"
          v-model="allLabelsMap[currLabelColor]"
        />
        <div class="form-btn">
          <button class="add-btn">Save</button>
        </div>
      </form>
    </main>
  </section>
</template>

<script>
export default {
  name: "label-picker",
  props: {
    selectedLabels: Array
  },
  data() {
    return {
      allLabelsMap: {
        "#eb5a46": "",
        "#0079bf": "",
        "#61bd4f": "",
        "#f2d600": "",
        "#ff9f1a": "",
        "#c377e0": "",
        "#0098b7": ""
      },
      isAddTitle: false,
      currLabelColor: null
    };
  },
  methods: {
    isSelectedLabel(labelColor) {
      const label = this.selectedLabels.find(
        selectedLabel => selectedLabel.color === labelColor
      );

      if (label) {
        this.allLabelsMap[labelColor] = label.title;
        return true;
      }

      return false;
    },
    updateLabels(labelColor) {
      let lableExist = false;

      this.selectedLabels.forEach(selectedLabel => {
        if (selectedLabel.color === labelColor) {
          lableExist = true;
          this.allLabelsMap[labelColor] = "";
          this.$emit("remove-label", selectedLabel);
        }
      });

      if (!lableExist) {
        const newSelectedLabel = {
          color: labelColor,
          title: ""
        };

        this.$emit("add-label", newSelectedLabel);
      }
    },
    toggleAddTitle(labelColor = null) {
      this.isAddTitle = !this.isAddTitle;
      this.currLabelColor = labelColor;
    },
    saveTitle() {
      const label = {
        color: this.currLabelColor,
        title: JSON.parse(
          JSON.stringify(this.allLabelsMap[this.currLabelColor])
        )
      };
      this.$emit("update-label", label);

      this.isAddTitle = !this.isAddTitle;
    }
  }
};
</script>