<template>
  <v-row align="center" class="list px-3 mx-auto">
    <v-col cols="12" sm="8">
      <v-text-field
        v-model="searchTitle"
        label="Search by Title"
      ></v-text-field>
    </v-col>

    <v-col cols="12" sm="4">
      <v-btn
        small
        @click="
          page = 1;
          retrieveTutorials();
        "
      >
        Search
      </v-btn>
    </v-col>

    <v-col cols="12" sm="12">
      <v-card class="mx-auto" tile>
        <v-card-title>Tutorials</v-card-title>

        <v-data-table
          :headers="headers"
          :items="tutorials"
          disable-pagination
          :hide-default-footer="true"
        >
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon small class="mr-2" @click="editTutorial(item.id)">
              mdi-pencil
            </v-icon>
            <v-icon small @click="deleteTutorial(item.id)">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>

        <v-card-actions v-if="tutorials.length > 0">
          <v-btn small color="error" @click="removeAllTutorials">
            Remove All
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>

    <v-col cols="12" sm="12">
      <v-row>
        <v-col cols="4" sm="3">
          <v-select
            v-model="pageSize"
            :items="pageSizes"
            label="Items per Page"
            @change="handlePageSizeChange"
          ></v-select>
        </v-col>

        <v-col cols="12" sm="9">
          <v-pagination
            v-model="page"
            :length="totalPages"
            total-visible="7"
            next-icon="mdi-menu-right"
            prev-icon="mdi-menu-left"
            @input="handlePageChange"
          ></v-pagination>
        </v-col>
      </v-row>
    </v-col>
  </v-row>
</template>

<script>
import TutorialDataService from "../services/TutorialDataService";
export default {
  name: "tutorials-list",
  data() {
    return {
      tutorials: [],
      searchTitle: "",
      headers: [
        { text: "Title", value: "title", sortable: false },
        { text: "Description", value: "description", sortable: false },
        { text: "Status", value: "status", sortable: false },
        { text: "Actions", value: "actions", sortable: false },
      ],
      page: 1,
      pageSize: 3,
      totalPages: 0,
      pageSizes: [3, 6, 9],
    };
  },
  methods: {
    getRequestParmas(searchTitle, page, pageSize) {
      let params = {};
      if (searchTitle) {
        params["title"] = searchTitle;
      }
      if (page) {
        params["page"] = page - 1;
      }
      if (pageSize) {
        params["size"] = pageSize;
      }
      return params;
    },
    retrieveTutorials() {
      const params = this.getRequestParmas(
        this.searchTitle,
        this.page,
        this.pageSize
      );
      TutorialDataService.getAll(params)
        .then((res) => {
          const { tutorials, totalPages } = res.data;
          this.tutorials = tutorials.map(this.getDisplay);
          this.totalPages = totalPages;
          console.log(res.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    handlePageChange(value) {
      this.page = value;
      this.retrieveTutorials();
    },
    handlePageSizeChange(size) {
      this.pageSize = size;
      this.page = 1;
      this.retrieveTutorials();
    },
    getDisplay(tutorial) {
      return {
        id: tutorial.id,
        title: tutorial.title.substr(0, 20) + "...",
        description: tutorial.description.substr(0, 20) + "...",
        status: tutorial.published ? "Published" : "Pending",
      };
    },
  },
  mounted() {
    this.retrieveTutorials();
  },
};
</script>

<style>
.list {
  max-width: 750px;
}
</style>
