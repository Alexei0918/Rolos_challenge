<template>
    <ul :class="containerClass" v-if="!noLiSurround">
      <li v-for="page in pages" :class="[pageClass, page.selected ? activeClass : '', page.disabled ? disabledClass : '', page.breakView ? breakViewClass: '']">
        <a v-if="page.breakView" :class="[pageLinkClass, breakViewLinkClass]" tabindex="0"><slot name="breakViewContent">{{ breakViewText }}</slot></a>
        <a v-else-if="page.disabled" :class="pageLinkClass" tabindex="0">{{ page.content }}</a>
        <a v-else @click="handlePageSelected(page.index + 1)" @keyup.enter="handlePageSelected(page.index + 1)" :class="pageLinkClass" tabindex="0">{{ page.content }}</a>
      </li>
    </ul>
  </template>
  
  <script>
  export default {
    props: {
      value: {
        type: Number
      },
      pageCount: {
        type: Number,
        required: true
      },
      forcePage: {
        type: Number
      },
      clickHandler: {
        type: Function,
        default: () => { }
      },
      pageRange: {
        type: Number,
        default: 3
      },
      marginPages: {
        type: Number,
        default: 1
      },
      breakViewText: {
        type: String,
        default: '…'
      },
      containerClass: {
        type: String
      },
      pageClass: {
        type: String
      },
      pageLinkClass: {
        type: String
      },
      breakViewClass: {
        type: String
      },
      breakViewLinkClass: {
        type: String
      },
      activeClass: {
        type: String,
        default: 'active'
      },
      disabledClass: {
        type: String,
        default: 'disabled'
      },
      noLiSurround: {
        type: Boolean,
        default: false
      }
    },
    beforeUpdate() {
      if (this.forcePage === undefined) return
      if (this.forcePage !== this.selected) {
        this.selected = this.forcePage
      }
    },
    computed: {
      selected: {
        get: function() {
          return this.value || this.innerValue
        },
        set: function(newValue) {
          this.innerValue = newValue
        }
      },
      pages: function () {
        let items = {}
        // console.log("selected: ", this.selected);
        if (this.pageCount <= this.pageRange) {
          for (let index = 0; index < this.pageCount; index++) {
            let page = {
              index: index,
              content: index + 1,
              selected: index === (this.selected - 1)
            }
            items[index] = page
          }
        } else {
          const halfPageRange = Math.floor(this.pageRange / 2)
  
          let setPageItem = index => {
            let page = {
            index: index,
            content: index + 1,
            selected: index === (this.selected - 1)
            }
            items[index] = page
          }
  
          let setBreakView = index => {
            let breakView = {
              disabled: true,
              breakView: true
            }
  
            items[index] = breakView
          }
  
          // 1st - loop through low end of margin pages
          for (let i = 0; i < this.marginPages; i++) {
            setPageItem(i);
          }
  
          // 2nd - loop through selected range
          let selectedRangeLow = 0;
          if (this.selected - halfPageRange > 0) {
            selectedRangeLow = this.selected - 1 - halfPageRange;
          }
  
          let selectedRangeHigh = selectedRangeLow + this.pageRange - 1;
          
          if(this.selected <= this.pageRange) {
            selectedRangeHigh = this.selected + 1;
          } else if (selectedRangeHigh >= this.pageCount) {
            selectedRangeHigh = this.pageCount - 1;
            selectedRangeLow = selectedRangeHigh - this.pageRange + 2;
            if(this.pageCount == this.selected) {
                selectedRangeLow = selectedRangeHigh - this.pageRange + 3;
            }
          }

          for (let i = selectedRangeLow - 1; i <= selectedRangeHigh && i <= this.pageCount - 1; i++) {
            if(i > 0) {
                setPageItem(i);
            }
          }
  
          // Check if there is breakView in the left of selected range
          if (selectedRangeLow > this.marginPages + 1) {
            setBreakView(selectedRangeLow - 1)
          }
  
          // Check if there is breakView in the right of selected range
          if (selectedRangeHigh + 1 < this.pageCount - this.marginPages) {
            setBreakView(selectedRangeHigh + 1)
          }
  
          // 3rd - loop through high end of margin pages
          if((this.pageCount - this.selected) < 2) {
            for (let i = this.pageCount - 1; i >= this.selected - 3; i--) {
                setPageItem(i);
            }
          } else {
            for (let i = this.pageCount - 1; i >= this.pageCount - this.marginPages; i--) {
                setPageItem(i);
            }
          }
        }
        return items
      }
    },
    data() {
      return {
        innerValue: 1,
      }
    },
    methods: {
      handlePageSelected(selected) {
        if (this.selected === selected) return
  
        this.innerValue = selected
        this.$emit('input', selected)
        this.clickHandler(selected)
      },
    }
  }
  </script>
  
  <style lang="css" scoped>
  a {
    cursor: pointer;
  }
  ul.pagination {
    display: flex;
    padding-left: 0px;
    margin-top: 0px;
  }
  li.page-item {
    list-style: none;
    padding: 5px;
    font-size: 20px;
    font-weight: bold;
  }
  li.page-item.active {
    color: red;
  }
  </style>