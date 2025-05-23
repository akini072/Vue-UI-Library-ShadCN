<template>
  <div class="d-flex align-center">
    <popover>
      <popover-trigger as="div">
        <v-button variant="outlined" class="border-dashed pl-2" height="36px">
          <v-button v-if="selected.length > 0" variant="text" icon @click.stop="handleClearFilter">
            <v-icon
              name="close-circle-outline"
              height="16px"
              color="var(--gray-500)"
            />
          </v-button>
          <v-icon
            v-else
            class="mx-2"
            name="plus-circle"
            height="14px"
            color="var(--gray-500)"
          />
          <span>{{ label }}</span>
          <div v-if="selected.length > 0" class="border-l pl-2 ml-2">
            <span
              v-if="selected.length > 2"
              class="mx-1 px-1 py-1 bg--gray-100 rounded-md"
            >
              {{ selected.length }} selected
            </span>
            <div v-else>
              <span
                v-for="select in selected"
                class="mr-1 px-1 py-1 bg--gray-100 rounded-sm font-weight-regular"
              >
                {{ select.label || select.value }}
              </span>
            </div>
          </div>
        </v-button>
      </popover-trigger>
      <popover-content class="p-0" style="width: 200" align="start">
        <div
          class="border-b d-flex align-center hover-bg--gray-100 px-3 py-2 search-field w-full"
        >
          <v-icon
            name="search"
            class="mr-2"
            height="14px"
            color="var(--gray-400)"
          />
          <input
            placeholder="Status"
            v-model="searchValue"
            class="hover-bg--gray-100 text--gray-500"
          />
        </div>
        <span
          class="d-flex justify-center align-center pa-4"
          v-if="searchValue && filterOptions.length == 0"
        >
          No results found
        </span>
        <template v-else>
          <div class="border-b">
            <div
              v-for="option in filterOptions"
              :key="option.value"
              class="hover-bg--gray-100 hover-text--gray-700 text--gray-600"
            >
              <label class="d-flex align-center flex-grow h-full px-3 py-2">
                <checkbox v-model:checked="option.checked" />
                <v-icon
                  v-if="option.icon"
                  :name="option.icon"
                  height="16px"
                  class="font-weight-medium ml-2"
                />
                <span class="ml-2">
                  {{ option.label || option.value }}
                </span>
                <h6 v-if="counts[option.value]" class="ml-auto text--gray-600">
                  {{ counts[option.value] }}
                </h6>
              </label>
            </div>
          </div>
          <div
            class="d-flex align-center text--gray-600 hover-bg--gray-100 px-3 py-2 justify-center pointer"
            @click="handleClearFilter"
          >
            Clear filters
          </div>
        </template>
      </popover-content>
    </popover>
  </div>
</template>
<script>
import { defineAsyncComponent } from 'vue'

export default {
  name: 'SelectStatus',
  components: {
    VIcon: defineAsyncComponent(() => import('@/components/base/VIcon')),
    VButton: defineAsyncComponent(() => import('@/components/base/VButton')),
    Popover: defineAsyncComponent(() =>
      import('@/components/shadcn/popover/Popover'),
    ),
    PopoverContent: defineAsyncComponent(() =>
      import('@/components/shadcn/popover/PopoverContent'),
    ),
    PopoverTrigger: defineAsyncComponent(() =>
      import('@/components/shadcn/popover/PopoverTrigger'),
    ),
    Checkbox: defineAsyncComponent(() =>
      import('@/components/shadcn/checkbox/Checkbox'),
    ),
  },
  props: {
    label: { type: String, required: true },
    options: {
      type: Array,
      default: () => [],
    },
    counts: {
      type: Object,
      default: {},
    },
  },
  emits: ['update:selected'],
  data() {
    return {
      searchValue: '',
    }
  },
  computed: {
    filterOptions() {
      return this.options.filter((option) =>
        option.value.toLowerCase().includes(this.searchValue.toLowerCase()),
      )
    },
    selected() {
      return this.options.filter(({ checked }) => checked)
    },
  },
  watch: {
    selected(value) {
      this.$emit('update:selected', value)
    },
  },
  methods: {
    handleClearFilter() {
      this.searchValue = ''
      this.options.forEach((option) => (option.checked = false))
    },
  },
}
</script>
<style lang="scss" scoped>
.border-dashed {
  border-style: dashed;
}

.text-sm {
  font-size: 12px;
}

.check-box {
  height: 16px;
  width: 16px;
}

.border-blank-check {
  border: 1px solid var(--gray-500);
}

.search-field {
  &:hover {
    & input {
      background-color: var(--gray-100);
    }
  }
}

.responsive-select {
  @media (max-width: 450px) {
    display: none;
  }
}
</style>
