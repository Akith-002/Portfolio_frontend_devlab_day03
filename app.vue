<template>
  <h1>{{ name }}</h1>

  <div v-if="pending">Loading Projects...</div>
  <div>
    <h2>Projects</h2>
    <ul>
      <li v-for="project in projects" :key="project.name">
        <h3>{{ project.name }}</h3>
        <p>{{ project.description }}</p>
      </li>
    </ul>
  </div>

  <!-- form to create new project -->
  <div>
    <h2>Create Project</h2>
    <form @submit.prevent="createProject">
      <div>
        <label for="name">Project Name : </label>
        <input type="text" v-model="newProject.name" id="name" required />
      </div>
      <div>
        <label for="description">Project Description : </label>
        <textarea
          name="description"
          v-model="newProject.description"
          id="description"
        ></textarea>
      </div>
      <button type="submit">Create Project</button>
    </form>
  </div>

  <!-- form to update project by id -->
  <div>
    <h2>Update Project</h2>
    <form @submit.prevent="updateProject">
      <div>
        <label for="id">Project ID : </label>
        <input type="text" v-model="update.id" id="id" required />
      </div>
      <div>
        <label for="name">Project Name : </label>
        <input type="text" v-model="update.name" id="name" required />
      </div>
      <div>
        <label for="description">Project Description : </label>
        <textarea
          name="description"
          v-model="update.description"
          id="description"
        ></textarea>
      </div>
      <button type="submit">Update Project</button>
    </form>
  </div>

  <!-- delete a form by id -->
  <div>
    <h2>Delete Project</h2>
    <form @submit.prevent="deleteProject">
      <div>
        <label for="id">Project ID : </label>
        <input type="text" v-model="deleteId" id="id" required />
      </div>
      <button type="submit">Delete Project</button>
    </form>
  </div>

  <div v-if="pending1">Loading Blogs...</div>
  <div>
    <h2>Blogs</h2>
    <ul>
      <li v-for="blog in blogs">
        <h3>{{ blog.title }}</h3>
        <p>{{ blog.content }}</p>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";

const name = "Akith Chandinu";
const {
  data: projects,
  pending,
  error,
} = useFetch("http://localhost:5000/projects");
const {
  data: blogs,
  pending1,
  error1,
} = useFetch("http://localhost:5000/blogs");

const newProject = ref({ name: "", description: "" });

const createProject = async () => {
  console.log(newProject.value);
  try {
    //ensure that newProject.value is a valid object
    const projectData = {
      name: newProject.value.name.trim(),
      description: newProject.value.description.trim(),
      index: projects.value.length + 1,
    };

    const response = await fetch("http://localhost:5000/projects", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(projectData),
    });

    if (!response.ok) {
      throw new Error("Failed to create project");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // add the new project to the projects array
    projects.value.push(data);

    // clear the newProject object
    newProject.value = { name: "", description: "" };
  } catch (error) {
    console.error(error);
  }
};

const update = ref({ id: "", name: "", description: "" });

const updateProject = async () => {
  console.log(update.value);
  try {
    //ensure that update.value is a valid object
    const projectData = {
      name: update.value.name.trim(),
      description: update.value.description.trim(),
    };

    const response = await fetch(
      `http://localhost:5000/projects/${update.value.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(projectData),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to update project");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // find the project in the projects array and update it
    const index = projects.value.findIndex(
      (project) => project._id === data._id
    );
    projects.value[index] = data;

    // clear the update object
    update.value = { id: "", name: "", description: "" };
  } catch (error) {
    console.error(error);
  }
};

const deleteId = ref("");

const deleteProject = async () => {
  console.log(deleteId.value);
  try {
    const response = await fetch(
      `http://localhost:5000/projects/${deleteId.value}`,
      {
        method: "DELETE",
      }
    );

    if (!response.ok) {
      throw new Error("Failed to delete project");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // remove the project from the projects array
    const index = projects.value.findIndex(
      (project) => project._id === data._id
    );
    projects.value.splice(index, 1);

    // clear the deleteId object
    deleteId.value = "";
  } catch (error) {
    console.error(error);
  }
};
</script>

<style scoped>
.hello {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 3rem;
  padding: 2rem;
}
</style>
