<template>
  <div>
    <!--    global search-->
    <div class="custom-search d-flex justify-content-end">
      <b-form-group>
        <div class="d-flex align-items-center">
          <label class="mr-1">Search</label>
          <b-form-input
            v-model="searchTerm"
            placeholder="Search"
            type="text"
            class="d-inline-block"
            @input="searchHandler"
          />
        </div>
      </b-form-group>
    </div>
    <!-- table -->
    <vue-good-table
      :columns="columns"
      :rows="rows.data ? rows.data :0"
      :is-loading="loader"
      :search-options="{
        skipDiacritics: true,
        enabled: true,
        externalQuery: searchTerm }"
      mode="remote"
      :pagination-options="{
        enabled: true,
        perPage:pageLength
      }"
      @on-column-filter="columnFilter"
    >
      <template
        slot="table-row"
        slot-scope="props"
      >

        <!-- Column: Name -->
        <div
          v-if="props.column.field === 'fullName'"
          class="text-nowrap"
        >
          <b-avatar
            :src="props.row.avatar"
            class="mx-1"
          />
          <span class="text-nowrap">{{ props.row.fullName }}</span>
        </div>

        <!-- Column: Status -->
        <span v-else-if="props.column.field === 'status'">
          <b-badge :variant="statusVariant(props.row.status)">
            {{ props.row.status }}
          </b-badge>
        </span>

        <!-- Column: Action -->
        <span v-else-if="props.column.field === 'action'">
          <span>
            <b-dropdown
              variant="link"
              toggle-class="text-decoration-none"
              no-caret
            >
              <template v-slot:button-content>
                <feather-icon
                  icon="MoreVerticalIcon"
                  size="16"
                  class="text-body align-middle mr-25"
                />
              </template>
              <b-dropdown-item>
                <feather-icon
                  icon="Edit2Icon"
                  class="mr-50"
                />
                <span>Edit</span>
              </b-dropdown-item>
              <b-dropdown-item>
                <feather-icon
                  icon="TrashIcon"
                  class="mr-50"
                />
                <span>Delete</span>
              </b-dropdown-item>
            </b-dropdown>
          </span>
        </span>

        <!-- Column: Common -->
        <span v-else>
          {{ props.formattedRow[props.column.field] }}
        </span>
      </template>

      <!-- pagination -->
      <template
        slot="pagination-bottom"
        slot-scope="props"
      >
        <div class="d-flex justify-content-between flex-wrap">
          <div class="d-flex align-items-center mb-0 mt-1">
            <span class="text-nowrap">
              Showing 1 to
            </span>
            <b-form-select
              v-model="pageLength"
              :options="['3','5','10']"
              class="mx-1"
              @input="(value)=>props.perPageChanged({currentPerPage:value})"
              @change="selectedPage(pageLength)"
            />
            <span class="text-nowrap "> of {{ rows.meta && rows.meta.total }} entries </span>
          </div>
          <div>
            <b-pagination
              :value="1"
              :total-rows="rows.meta && rows.meta.total"
              :per-page="pageLength"
              first-number
              last-number
              align="right"
              prev-class="prev-item"
              next-class="next-item"
              class="mt-1 mb-0"
              @change="handleChangePage"
            >
              <template #prev-text>
                <feather-icon
                  icon="ChevronLeftIcon"
                  size="18"
                />
              </template>
              <template #next-text>
                <feather-icon
                  icon="ChevronRightIcon"
                  size="18"
                />
              </template>
            </b-pagination>
          </div>
        </div>
      </template>
    </vue-good-table>
  </div>
</template>

<script>
import { VueGoodTable } from 'vue-good-table'
import 'vue-good-table/dist/vue-good-table.css'
import {
  BAvatar,
  BBadge,
  BDropdown,
  BDropdownItem,
  BFormSelect,
  BPagination,
} from 'bootstrap-vue'

export default {
  name: 'VueGoodTable',
  components: {
    VueGoodTable,
    BAvatar,
    BBadge,
    BPagination,
    BFormSelect,
    BDropdown,
    BDropdownItem,
  },
  data() {
    return {
      loader: false,
      pageLength: 10,
      dir: false,
      columns: [
        {
          label: 'Name',
          field: 'name',
          filterOptions: {
            enabled: true,
            placeholder: 'Search Name',
          },
        },

        {
          label: 'Status',
          field: 'status',
          filterOptions: {
            enabled: true,
            placeholder: 'Search Status',
            filterDropdownItems: ['active', 'inactive'],
          },
        },
        {
          label: 'Action',
          field: 'action',
        },
      ],
      searchTerm: '',
      page: 1,
      data: {},
    }
  },
  computed: {
    statusVariant() {
      const statusColor = {
        /* eslint-disable key-spacing */
        Current      : 'light-primary',
        Professional : 'light-success',
        Rejected     : 'light-danger',
        Resigned     : 'light-warning',
        Applied      : 'light-info',
        /* eslint-enable key-spacing */
      }

      return status => statusColor[status]
    },
    rows() {
      return this.$store.getters['getter-url']
    },
  },
  mounted() {
    this.getDataList()
  },
  methods: {
    columnFilter(params) {
      this.getDataList(params.columnFilters)
    },
    getDataList(data = null, current_page = 1) {
      this.loader = true
      document.onkeydown = () => false
      // this.$store.dispatch('dispatch-url', {
      //   params: {
      //     page: current_page,
      //     per_page: this.pageLength,
      //     searchTerm: this.searchTerm,
      //     name: data ? data.name : '',
      //     email: data ? data.email : '',
      //     status: data ? data.status : '',
      //   },
      // }).then(() => {
      //   this.loader = false
      //   document.onkeydown = () => true
      // })
    },
    handleChangePage(page) {
      this.getDataList(page)
    },
    selectedPage(value) {
      this.getDataList(this.page, value)
    },
    searchHandler() {
      this.getDataList(this.page, this.pageLength, this.searchTerm)
    },
  },
}
</script>

<style scoped>

</style>
