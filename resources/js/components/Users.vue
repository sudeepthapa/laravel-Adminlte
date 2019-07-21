<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-md-12">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Our Users</h3>

            <div class="card-tools">
              <button class="btn btn-success" @click="newModal">
                <i class="fa fa-user-plus fa-fw"></i>Add User
              </button>
            </div>
          </div>
          <!-- /.card-header -->
          <div class="card-body table-responsive p-0">
            <table class="table table-hover">
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
                <tr v-for="user in users.data" :key="user.id">
                  <td>{{ user.id }}</td>
                  <td>{{ user.name }}</td>
                  <td>{{ user.email }}</td>
                  <td>{{ user.type | upText}}</td>
                  <td>{{ user.created_at | myDate }}</td>
                  <td>
                    <a href="#" @click="editModal(user)">
                      <i class="fa fa-edit blue"></i>
                    </a> &nbsp;&nbsp;
                    <a href="#" @click="deleteUser(user.id)">
                      <i class="fa fa-trash red"></i>
                    </a> &nbsp;&nbsp;
                    <a href="#">
                      <i class="fa fa-eye teal"></i>
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

    <div
      class="modal fade"
      id="addnewusermodal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="addnewusermodal"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="addnewusermodal">{{editMode ? "Edit User":"Add User"}}</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <form @submit.prevent="editMode ? updateUser() : createUser()">
            <div class="modal-body">
              <div class="form-group">
                <input
                  v-model="form.name"
                  type="text"
                  placeholder="Name"
                  name="name"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('name') }"
                />
                <has-error :form="form" field="name"></has-error>
              </div>
              <div class="form-group">
                <input
                  v-model="form.email"
                  type="text"
                  placeholder="Email Address"
                  name="email"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('email') }"
                />
                <has-error :form="form" field="email"></has-error>
              </div>
              <div class="form-group">
                <textarea
                  v-model="form.bio"
                  placeholder="Short Bio"
                  name="bio"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('bio') }"
                ></textarea>
                <has-error :form="form" field="bio"></has-error>
              </div>
              <div class="form-group">
                <select
                  name="type"
                  v-model="form.type"
                  id="type"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('type') }"
                >
                  <option value>Select User Role</option>
                  <option value="admin">Admin</option>
                  <option value="user">User</option>
                  <option value="author">Author</option>
                </select>
                <has-error :form="form" field="type"></has-error>
              </div>
              <div class="form-group">
                <input
                  v-model="form.password"
                  type="password"
                  placeholder="Password"
                  name="password"
                  class="form-control"
                  :class="{ 'is-invalid': form.errors.has('password') }"
                />
                <has-error :form="form" field="password"></has-error>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
              <button
                :disabled="form.busy"
                type="submit"
                class="btn btn-primary"
              >{{ !editMode ? "Create":"Update" }}</button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Modal -->
  </div>
</template>

<script>
import { setInterval } from "timers";
export default {
  data() {
    return {
      editMode: true,
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
    updateUser() {
      console.log("Edit Users");
    },

    editModal(user) {
      this.form.reset();
      this.form.clear();

      this.editMode = true;
      $("#addnewusermodal").modal("show");
      this.form.fill(user);
    },
    newModal() {
      this.form.reset();
      this.form.clear();
      this.editMode = false;
      $("#addnewusermodal").modal("show");
    },
    loadUsers() {
      axios.get("api/user").then(({ data }) => (this.users = data));
    },
    createUser() {
      this.$Progress.start();
      this.form
        .post("api/user")
        .then(user => {
          Fire.$emit("AfterCreated");
          this.$Progress.finish();
          $("#addnewusermodal").modal("hide");
          Toast.fire({
            type: "success",
            title: "User Created Successfully"
          });
        })
        .catch(err => {
          Toast.fire({
            type: "error",
            title: "Error Adding User"
          });
          this.$Progress.finish();
        });
    },

    deleteUser(id) {
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!"
      }).then(result => {
        //send request to server
        if (result.value) {
          this.$Progress.start();

          this.form
            .delete("api/user/" + id)
            .then(result => {
              Fire.$emit("AfterDeleted");
              Swal.fire("Deleted!", "Your file has been deleted.", "success");
              this.$Progress.finish();
              $("#addnewusermodal").modal("hide");
            })
            .catch(err => {
              this.$Progress.finish();

              Swal.fire("Error!", "Something went wrong.", "error");
            });
        }
      });
    }
  },

  mounted() {
    console.log("Component mounted.");
  },
  created() {
    this.loadUsers();
    Fire.$on("AfterCreated", this.loadUsers);
    Fire.$on("AfterDeleted", this.loadUsers);
    // setInterval(()=>this.loadUsers(), 2000);
  }
};
</script>
