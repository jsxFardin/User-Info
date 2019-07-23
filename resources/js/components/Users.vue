<template>
  <div class="container-fluid">
    <div class="card">
      <div class="card-header">
        <h3 class="card-title pull-left mt-2">Users Table</h3>
        <div class="card-tools mt-2">
          <!-- Button trigger modal -->
          <button class="btn btn-success pull-right" @click="newModal">
            <i class="fa fa-user-plus"></i> Add User
          </button>
        </div>
      </div>
      <!-- /.card-header -->
      <div class="card-body">
        <table class="table table-bordered">
          <tbody>
            <tr>
              <th>SL</th>
              <th>Name</th>
              <th>Email</th>
              <th>Bio</th>
              <th>User Type</th>
              <th>Created Date</th>
              <th>#</th>
            </tr>
            <tr v-for="(user, index) in users" :key="user.id">
              <td>{{ ++index }}</td>
              <td>{{ user.name ? user.name : 'N/A' | textUpper }}</td>
              <td>{{ user.email ? user.email : 'N/A'}}</td>
              <td>{{ user.bio ? user.bio : 'N/A' }}</td>
              <td>{{ user.type ? user.type : 'N/A' | textUpper }}</td>
              <td>{{ user.created_at ? user.created_at : 'N/A' | dateFormat }}</td>
              <td>
                <a href="#" @click="editUser(user)" class="mr-2">
                  <i class="fa fa-edit"></i>
                </a>
                <a href="#" @click="deleteUser(user.id)" class="red">
                  <i class="fa fa-trash"></i>
                </a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <modal>
        <!--<modal v-else="!editMode" modalHeading="Update User">-->
        <form @submit.prevent="editMode ? updateUser() : createUser()">
          <div class="modal-body">
            <div class="form-group">
              <input
                v-model="form.name"
                type="text"
                name="name"
                class="form-control"
                :class="{ 'is-invalid': form.errors.has('name') }"
                placeholder="User name"
              />
              <has-error :form="form" field="name"></has-error>
            </div>

            <div class="form-group">
              <input
                v-model="form.email"
                type="email"
                name="email"
                class="form-control"
                :class="{ 'is-invalid': form.errors.has('email') }"
                placeholder="Email address"
              />
              <has-error :form="form" field="email"></has-error>
            </div>

            <div class="form-group">
              <textarea
                v-model="form.bio"
                name="bio"
                class="form-control"
                :class="{ 'is-invalid': form.errors.has('bio') }"
                placeholder="Bio"
              ></textarea>
              <has-error :form="form" field="bio"></has-error>
            </div>

            <div class="form-group">
              <select
                v-model="form.type"
                class="form-control"
                name="type"
                :class="{ 'is-invalid': form.errors.has('type') }"
              >
                <option selected="true">-Select user role-</option>
                <option>Admin</option>
                <option>Stander User</option>
                <option>Author</option>
              </select>
              <has-error :form="form" field="type"></has-error>
              <!--<input v-model="form.name" type="text" name="type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }" placeholder="Type user">-->
            </div>

            <div class="form-group">
              <input
                v-model="form.password"
                type="password"
                name="password"
                v-show="!editMode"
                class="form-control"
                :class="{ 'is-invalid': form.errors.has('password') }"
                placeholder="Password"
              />
              <has-error :form="form" field="password"></has-error>
            </div>
          </div>

          <div class="modal-footer">
            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
            <button type="submit" v-show="!editMode" class="btn btn-primary">Create</button>
            <button type="submit" v-show="editMode" class="btn btn-success">Update</button>
          </div>
        </form>
      </modal>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      editMode: false,
      editMode: false,
      users: {},
      form: new Form({
        id: "",
        name: "",
        email: "",
        bio: "",
        type: "",
        photo: "",
        password: ""
      })
    };
  },
  mounted() {
    this.loadUsers();
    Fire.$on("AfterResReq", () => {
      this.loadUsers();
    });
  },
  methods: {
    newModal() {
      $("#modalTitle").html("New User");
      this.form.clear();
      this.form.reset();
      $("#globalModal").modal("show");
      this.editMode = false;
    },
    editUser(user) {
      $("#globalModal").modal("show");
      $("#modalTitle").html("User Update Info");
      this.form.clear();
      this.form.fill(user);
      this.editMode = true;
    },

    updateUser() {
      this.form
        .put("api/user/" + this.form.id)
        .then(() => {
          this.$Progress.start();
          Fire.$emit("AfterResReq");
          this.$Progress.finish();
          Toast.fire({
            type: "success",
            title: "User info updated!"
          });
          $("#globalModal").modal("hide");
        })
        .catch(() => {
          this.$Progress.fail();
        });
    },

    loadUsers() {
      // this.form.get('/api/user')
      axios.get("api/user").then(({ data }) => (this.users = data));
    },

    createUser() {
      this.$Progress.start();
      this.form
        .post("/api/user")
        .then(() => {
          this.$Progress.finish();
          Toast.fire({
            type: "success",
            title: "User create Successfully"
          });
          $("#globalModal").modal("hide");
          this.form.reset();
          this.loadUsers();
        })
        .catch(() => {
          this.$Progress.fail();
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
        if (result.value) {
          this.form
            .delete("api/user/" + id)
            .then(() => {
              Fire.$emit("AfterResReq");
              Swal.fire("Deleted!", "Your file has been deleted.", "success");
            })
            .catch(() => {});
        }
      });
    }
  }
  /* created() {
    this.loadUsers();
    Fire.$on("AfterResReq", () => {
      this.loadUsers();
    });
  } */
};
</script>

<style scoped>
</style>
