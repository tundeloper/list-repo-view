<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <ul>
          <li v-for="repo in paginatedRepos" :key="repo.id">
            <strong>{{ repo.name }}</strong>: {{ repo.description }}
          </li>
        </ul>
        <div class="pagination-controls">
          <v-btn @click="prevPage" :disabled="page === 1" class="pagination-btn">Previous</v-btn>
          <v-btn @click="nextPage" :disabled="page >= totalPages" class="pagination-btn">Next</v-btn>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "RepoList",
  data() {
    return {
      repos: [],
      page: 1,
      itemsPerPage: 5,
      error: null,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.repos.length / this.itemsPerPage);
    },
    paginatedRepos() {
      const start = (this.page - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.repos.slice(start, end);
    }
  },
  methods: {
    nextPage() {
      if (this.page < this.totalPages) {
        this.page++;
      }
    },
    prevPage() {
      if (this.page > 1) {
        this.page--;
      }
    }
  },
  created() {
    fetch("https://api.github.com/users/tundeloper/repos")
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok ' + response.statusText);
        }
        return response.json();
      })
      .then(data => {
        if (Array.isArray(data)) {
          this.repos = data;
        } else {
          throw new Error('Data format is not as expected');
        }
      })
      .catch(error => {
        console.error("Error fetching repositories:", error);
        this.error = error;
      });
  }
};
</script>

<style scoped>
.pagination-controls {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
}

.pagination-btn {
  transition: background-color 0.3s, color 0.3s;
}


.pagination-btn:hover {
  background-color: #1976d2;
  color: white;
  cursor: pointer;
}
</style>