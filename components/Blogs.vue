<template>
  <div>
    <div>
      <div v-if="pending1">Loading Blogs...</div>
      <h2>Blogs</h2>
      <ul>
        <li v-for="blog in blogs" :key="blog.title">
          <h3>{{ blog.title }}</h3>
          <p>{{ blog.content }}</p>
        </li>
      </ul>
    </div>

    <!-- form to create new blog -->
    <!-- <div class="my-4">
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
    </div> -->

    <!-- form to update blog by id -->
    <!-- <div class="my-4">
      <h2>Update Blog</h2>
      <form @submit.prevent="updateBlog" class="form-group">
        <div class="mb-3">
          <label for="id" class="form-label">Blog Title:</label>
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
    </div> -->

    <!-- form to delete blog by id -->
    <!-- <div class="my-4">
      <h2>Delete Blog</h2>
      <form @submit.prevent="deleteBlog" class="form-group">
        <div class="mb-3">
          <label for="id" class="form-label">Blog Title:</label>
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
  </div> -->
  </div>
</template>

<script setup>
const {
  data: blogs,
  pending1,
  error1,
} = useFetch("http://localhost:5000/blogs");

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
