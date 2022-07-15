<template>
  <div v-if="currentTutorial" class="edit-form py-3">
    <p class="headline">Edit Tutorial</p>

    <v-form ref="form" v-model="valid" lazy-validation>
      <v-text-field
        v-model="currentTutorial.title"
        :rules="[(v) => !!v || 'Title is required']"
        label="Title"
        required
      ></v-text-field>

      <v-text-field
        v-model="currentTutorial.description"
        :rules="[(v) => !!v || 'Description is required']"
        label="Description"
        required
      ></v-text-field>

      <label><strong>Status:</strong></label>
      {{ currentTutorial.published ? "Published" : "Pending" }}

      <v-divider class="my-5"></v-divider>

      <v-btn
        v-if="currentTutorial.published"
        @click="updatePublished(false)"
        color="primary"
        small
        class="mr-2"
      >
        UnPublish
      </v-btn>

      <v-btn
        v-else
        @click="updatePublished(true)"
        color="primary"
        small
        class="mr-2"
      >
        Publish
      </v-btn>

      <v-btn color="error" small class="mr-2" @click="deleteTutorial">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateTutorial">
        Update
      </v-btn>
    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <br />
    <p>Please click on a Tutorial...</p>
  </div>
</template>

<script>
export default {
  name: "tutorial",
};
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
</style>
