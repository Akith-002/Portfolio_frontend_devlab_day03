<template>
  <div class="container">
    <h1>{{ name }}</h1>

    <!-- Projects -->
    <div class="my-4">
      <div v-if="pending" class="alert alert-info">Loading Projects...</div>
      <h2>Projects</h2>
      <ul class="list-group">
        <li
          v-for="project in projects"
          :key="project.name"
          class="list-group-item"
        >
          <h3>{{ project.name }}</h3>
          <p>{{ project.description }}</p>
        </li>
      </ul>
    </div>

    <!-- form to create new project -->
    <div class="my-4">
      <h2>Create Project</h2>
      <form @submit.prevent="createProject" class="form-group">
        <div class="mb-3">
          <label for="name" class="form-label">Project Name:</label>
          <input
            type="text"
            v-model="newProject.name"
            id="name"
            class="form-control"
            required
          />
        </div>
        <div class="mb-3">
          <label for="description" class="form-label"
            >Project Description:</label
          >
          <textarea
            name="description"
            v-model="newProject.description"
            id="description"
            class="form-control"
          ></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Create Project</button>
      </form>
    </div>

    <!-- form to update project by id -->
    <div class="my-4">
      <h2>Update Project</h2>
      <form @submit.prevent="updateProject" class="form-group">
        <div class="mb-3">
          <label for="name" class="form-label">Project name:</label>
          <!-- select the project using a dropdown -->
          <select v-model="update.id" class="form-select" required>
            <option value="">Select Project</option>
            <option v-for="project in projects" :value="project._id">
              {{ project.name }}
            </option>
          </select>
        </div>
        <div class="mb-3">
          <label for="name" class="form-label">Project Name:</label>
          <input
            type="text"
            v-model="update.name"
            id="name"
            class="form-control"
            required
          />
        </div>
        <div class="mb-3">
          <label for="description" class="form-label"
            >Project Description:</label
          >
          <textarea
            name="description"
            v-model="update.description"
            id="description"
            class="form-control"
          ></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Update Project</button>
      </form>
    </div>

    <!-- delete a form by id -->
    <div class="my-4">
      <h2>Delete Project</h2>
      <form @submit.prevent="deleteProject" class="form-group">
        <div class="mb-3">
          <label for="id" class="form-label">Project name:</label>
          <!-- select the project using a dropdown -->
          <select v-model="deleteId" id="id" class="form-select" required>
            <option value="">Select Project</option>
            <option v-for="project in projects" :value="project._id">
              {{ project.name }}
            </option>
          </select>
        </div>
        <button type="submit" class="btn btn-danger">Delete Project</button>
      </form>
    </div>

    <!-- Blogs -->
    <div class="my-4">
      <div v-if="pending1" class="alert alert-info">Loading Blogs...</div>
      <h2>Blogs</h2>
      <ul class="list-group">
        <li v-for="blog in blogs" :key="blog.title" class="list-group-item">
          <h3>{{ blog.title }}</h3>
          <p>{{ blog.content }}</p>
        </li>
      </ul>
    </div>

    <!-- form to create new blog -->
    <div class="my-4">
      <h2>Create Blog</h2>
      <form @submit.prevent="createBlog" class="form-group">
        <div class="mb-3">
          <label for="title" class="form-label">Blog Title:</label>
          <input
            type="text"
            v-model="newBlog.title"
            id="title"
            class="form-control"
            required
          />
        </div>
        <div class="mb-3">
          <label for="content" class="form-label">Blog Content:</label>
          <textarea
            name="content"
            v-model="newBlog.content"
            id="content"
            class="form-control"
          ></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Create Blog</button>
      </form>
    </div>

    <!-- form to update blog by id -->
    <div class="my-4">
      <h2>Update Blog</h2>
      <form @submit.prevent="updateBlog" class="form-group">
        <div class="mb-3">
          <label for="id" class="form-label">Blog Title:</label>
          <!-- select the blog using a dropdown -->
          <select v-model="upBlog.id" class="form-select" required>
            <option value="">Select Blog</option>
            <option v-for="blog in blogs" :value="blog._id">
              {{ blog.title }}
            </option>
          </select>
        </div>
        <div class="mb-3">
          <label for="title" class="form-label">Blog Title:</label>
          <input
            type="text"
            v-model="upBlog.title"
            id="title"
            class="form-control"
            required
          />
        </div>
        <div class="mb-3">
          <label for="content" class="form-label">Blog Content:</label>
          <textarea
            name="content"
            v-model="upBlog.content"
            id="content"
            class="form-control"
          ></textarea>
        </div>
        <button type="submit" class="btn btn-primary">Update Blog</button>
      </form>
    </div>

    <!-- form to delete blog by id -->
    <div class="my-4">
      <h2>Delete Blog</h2>
      <form @submit.prevent="deleteBlog" class="form-group">
        <div class="mb-3">
          <label for="id" class="form-label">Blog Title:</label>
          <!-- select the blog using a dropdown -->
          <select v-model="deleteBlogId" id="id" class="form-select" required>
            <option value="">Select Blog</option>
            <option v-for="blog in blogs" :value="blog._id">
              {{ blog.title }}
            </option>
          </select>
        </div>
        <button type="submit" class="btn btn-danger">Delete Blog</button>
      </form>
    </div>
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

// create new blog
const newBlog = ref({ title: "", content: "" });

const createBlog = async () => {
  console.log(newBlog.value);
  try {
    //ensure that newBlog.value is a valid object
    const blogData = {
      title: newBlog.value.title.trim(),
      content: newBlog.value.content.trim(),
    };

    const response = await fetch("http://localhost:5000/blogs", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(blogData),
    });

    if (!response.ok) {
      throw new Error("Failed to create blog");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // add the new blog to the blogs array
    blogs.value.push(data);

    // clear the newBlog object
    newBlog.value = { title: "", content: "" };
  } catch (error) {
    console.error(error);
  }
};

// update blog by id
const upBlog = ref({ title: "", content: "" });

const updateBlog = async () => {
  console.log(upBlog.value);
  try {
    //ensure that upBlog.value is a valid object
    const blogData = {
      title: upBlog.value.title.trim(),
      content: upBlog.value.content.trim(),
    };

    const response = await fetch(
      `http://localhost:5000/blogs/${upBlog.value.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(blogData),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to update blog");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // find the blog in the blogs array and update it
    const index = blogs.value.findIndex((blog) => blog._id === data._id);
    blogs.value[index] = data;

    // clear the upBlog object
    upBlog.value = { id: "", title: "", content: "" };
  } catch (error) {
    console.error(error);
  }
};

// delete blog by id
const deleteBlogId = ref("");

const deleteBlog = async () => {
  console.log(deleteBlogId.value);
  try {
    const response = await fetch(
      `http://localhost:5000/blogs/${deleteBlogId.value}`,
      {
        method: "DELETE",
      }
    );

    if (!response.ok) {
      throw new Error("Failed to delete blog");
    }

    // Log the raw response text
    const responseText = await response.text();
    console.log("Raw Response:", responseText);

    // Try parsing the JSON response
    const data = JSON.parse(responseText);

    // replace the blog from the blogs array in frontend
    const index = blogs.value.findIndex((blog) => blog._id === data._id);
    blogs.value.splice(id, 1);

    // clear the deleteBlogId object
    deleteBlogId.value = "";
  } catch (error) {
    console.error(error);
  }
};
</script>

<style scoped>
@import "https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css";

.hello {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 3rem;
  padding: 2rem;
}
</style>
