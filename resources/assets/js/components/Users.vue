<template>
  <div class="container">
    <div class="row">
      <div class="col-md-12">
        <div class="card card-primary card-outline mt-4">
          <div class="card-header">
            <h3 class="card-title">Users List</h3>

            <div class="card-tools">
              <button class="btn btn-success" data-toggle="modal" data-target="#addNew">
                <i class="fa fa-user-plus"></i> Add New Users
              </button>
            </div>
          </div>
          <!-- /.card-header -->
          <div class="card-body table-responsive p-0">
            <table class="table table-hover text-nowrap">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Registered At</th>
                  <th>Modify</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="user in users" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name | upText}}</td>
                  <td>{{user.email}}</td>
                  <td>
                    <span class="badge badge-dark">{{user.type | upText}}</span>
                  </td>
                  <td>
                    <span class="badge badge-secondary">{{user.created_at | myDate}}</span>
                  </td>
                  <td>
                    <a href="#" class="btn btn-sm btn-primary">
                      <i class="fa fa-edit"></i> Edit
                    </a>

                    <a href="#" class="btn btn-sm btn-danger">
                      <i class="fa fa-trash"></i> Delete
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- /.card-body -->
        </div>
        <!-- /.card -->
      </div>
    </div>

    <!-- Modal -->
    <div class="modal fade" id="addNew" style="display: none;" aria-hidden="true">
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title">Add New</h4>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <form @submit.prevent="createUser">
            <div class="modal-body">
              <div class="form-group">
                <input
                  v-model="form.name"
                  type="text"
                  name="name"
                  placeholder="Name"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('name') }"
                />
                <has-error :form="form" field="name"></has-error>
              </div>

              <div class="form-group">
                <input
                  v-model="form.email"
                  type="email"
                  name="email"
                  placeholder="Email Address"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('email') }"
                />
                <has-error :form="form" field="email"></has-error>
              </div>

              <div class="form-group">
                <textarea
                  v-model="form.bio"
                  type="text"
                  name="bio"
                  placeholder="Keterangan diri (Opsional)"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('bio') }"
                ></textarea>
                <has-error :form="form" field="bio"></has-error>
              </div>

              <div class="form-group">
                <select
                  v-model="form.type"
                  type="text"
                  name="type"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('type') }"
                >
                  <option value>Select User Role</option>
                  <option value="admin">Admin</option>
                  <option value="user">Standart User</option>
                  <option value="author">Author</option>
                </select>
                <has-error :form="form" field="type"></has-error>
              </div>

              <div class="form-group">
                <input
                  v-model="form.password"
                  type="password"
                  name="password"
                  placeholder="Password"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('password') }"
                />
                <has-error :form="form" field="password"></has-error>
              </div>
            </div>
            <div class="modal-footer justify-content-between">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
              <button type="submit" class="btn btn-success">
                <i class="fa fa-save fa-fw"></i> Create
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Form from "vform";

export default {
  data() {
    return {
      users: {},
      form: new Form({
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    };
  },

  methods: {
    loadUsers() {
      this.$Progress.start();
      axios.get("api/user").then(({ data }) => (this.users = data.data));
      this.$Progress.finish();
    },
    createUser() {
      this.$Progress.start();
      this.form.post("api/user");
      $("#addNew").modal("hide");
      Toast.fire({
        icon: "success",
        title: "User created successfully"
      });
      Fire.$emit("afterCreate");
      this.$Progress.finish();
    }
  },

  created() {
    this.loadUsers();
    Fire.$on("afterCreate", () => {
      this.loadUsers();
    });
  }
};
</script>
