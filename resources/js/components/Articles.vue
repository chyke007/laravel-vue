<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <!-- Modal -->
        <div
          class="modal fade"
          id="exampleModal"
          tabindex="-1"
          role="dialog"
          aria-labelledby="exampleModalLabel"
          aria-hidden="true"
        >
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">
                  <b>Add Article</b>
                </h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <div class="modal-body">
                <div class="d-flex justify-content-center">
                  <form @submit.prevent="addArticle" class="mb-3">
                    <div class="form-group">
                      <input
                        type="text"
                        class="form-control"
                        placeholder="Title"
                        v-model="article.title"
                      >
                    </div>
                    <div class="form-group">
                      <textarea
                        class="form-control"
                        placeholder="Body"
                        v-model="article.body"
                        cols="30"
                        rows="10"
                      ></textarea>
                    </div>
                    <button
                      type="submit"
                      data-dismiss="modal"
                      @click="addArticle()"
                      class="btn btn-info"
                    >Save</button>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="d-flex justify-content-center" v-if="loading">
          <div class="spinner-border" role="status">
            <span class="sr-only">Loading...</span>
          </div>
        </div>

        <div v-else>
          <nav aria-label="Page navigation example">
            <ul class="pagination">
              <li :class="[{disabled :!pagination.prev_page_url}]" class="page-item">
                <a
                  class="page-link"
                  href="#"
                  @click="fetchArticles(pagination.prev_page_url)"
                  aria-label="Previous"
                >
                  <span aria-hidden="true">&laquo;</span>
                </a>
              </li>
              <li class="page-item disabled">
                <a
                  class="page-link disabled"
                  href="#"
                >{{pagination.current_page}} of {{pagination.last_page}}</a>
              </li>

              <li :class="[{disabled :!pagination.next_page_url}]" class="page-item">
                <a
                  class="page-link"
                  href="#"
                  @click="fetchArticles(pagination.next_page_url)"
                  aria-label="Next"
                >
                  <span aria-hidden="true">&raquo;</span>
                </a>
              </li>
              <li style="margin:0 auto;">
                <button
                  type="button"
                  class="btn btn-primary"
                  data-toggle="modal"
                  data-target="#exampleModal"
                >Add Article +</button>
              </li>
            </ul>
          </nav>

          <div
            class="alert alert-dismissible fade show"
            :class="{'btn-danger':message.danger,'btn-success':message.success}"
            v-if="message.status"
            role="alert"
          >
            <strong>Holy guacamole!</strong>
            {{message.content}}
            <button
              type="button"
              class="close"
              data-dismiss="alert"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="card">
            <div class="card-body" v-for="article in articles" v-bind:key="article.id">
              <h3>
                <b>{{article.title}}</b>
              </h3>
              <p>{{article.body}}</p>
              <button
                @click="editArticle(article)"
                type="button"
                class="btn btn-warning"
                data-toggle="modal"
                data-target="#exampleModal"
              >Edit</button>

              <button @click="deleteArticle(article.id)" type="button" class="btn btn-danger">Delete</button>
            </div>
          </div>
          <br>
          <br>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      article: {
        id: "",
        title: "",
        body: ""
      },
      article_id: "",
      pagination: {},
      edit: false,
      loading: true,
      message: {
        status: false,
        content: "",
        danger: false,
        success: true
      }
    };
  },
  created() {
    this.fetchArticles();
  },
  methods: {
    fetchArticles(page_url) {
      let vm = this;
      this.loading = true;
      page_url = page_url || "api/articles/";
      fetch(page_url)
        .then(res => res.json())
        .then(res => {
          this.loading = false;
          this.articles = res.data;
          this.hideAlert();
          vm.makePagination(res.meta, res.links);
        })
        .catch(err => {
          console.log(err);
          this.showMessage(
            "An error occured, please try again later.",
            false,
            true,
            true
          );
          this.loading = false;
          this.hideAlert();
        });
    },
    makePagination(meta, links) {
      let pagination = {
        current_page: meta.current_page,
        last_page: meta.last_page,
        next_page_url: links.next,
        prev_page_url: links.prev
      };

      this.pagination = pagination;
    },
    deleteArticle(id) {
      if (confirm("Are you sure?")) {
        this.loading = true;
        fetch(`api/article/${id}`, {
          method: "delete"
        })
          .then(res => res.json)
          .then(data => {
            this.message.content = "Article has been removed";
            this.showMessage("Article has been removed.", true, false, true);

            this.fetchArticles();
          })
          .catch(err => {
            console.log(err);

            this.showMessage(
              "An error occured, please try again later.",
              false,
              true,
              true
            );
            this.loading = false;
            this.hideAlert();
          });
      }
    },
    hideAlert() {
      setTimeout(res => (this.message.status = false), 9000);
    },
    showMessage(content, success, danger, status) {
      this.message.content = content;
      this.message.success = success;
      this.message.danger = danger;
      this.message.status = status;
    },
    performCheck() {
      if (this.article.title == "" || this.article.body == "") {
        this.showMessage("All fields are required.", false, true, true);
        return false;
      } else {
        return true;
      }
    },
    addArticle() {
      if (!this.performCheck()) {
        return;
      }
      this.loading = true;
      if (this.edit === false) {
        //Add
        fetch("api/article", {
          method: "post",
          body: JSON.stringify(this.article),
          headers: {
            "content-type": "application/json"
          }
        })
          .then(res => res.json())
          .then(data => {
            this.article.title = "";
            this.article.body = "";
            this.showMessage("Article has been added.", true, false, true);

            this.fetchArticles();
          })
          .catch(err => {
            console.log(err);

            this.showMessage(
              "An error occured, please try again later.",
              false,
              true,
              true
            );

            this.loading = false;
            this.hideAlert();
          });
      } else {
        //Edit
        fetch("api/article", {
          method: "put",
          body: JSON.stringify(this.article),
          headers: {
            "content-type": "application/json"
          }
        })
          .then(res => res.json())
          .then(data => {
            this.article.title = "";
            this.article.body = "";

            this.showMessage("Article has been updated.", true, false, true);

            this.fetchArticles();
          })
          .catch(err => {
            console.log(err);

            this.showMessage(
              "An error occured, please try again later.",
              false,
              true,
              true
            );

            this.loading = false;
            this.hideAlert();
          });
      }
    },
    editArticle(article) {
      this.edit = true;

      this.article.article_id = article.id;
      this.article.title = article.title;
      this.article.body = article.body;
    }
  },

  mounted() {}
};
</script>
