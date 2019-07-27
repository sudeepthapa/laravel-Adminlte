<template>
  <div class="container">
    <div class="row mt-3">
      <div class="col-md-12">
        <div class="card card-widget widget-user">
          <!-- Add the bg color to the header using any of the bg-* classes -->
          <div
            class="widget-user-header text-white"
            style="background: url('img/showcase.jpg') center center;height:300px"
          >
            <h3 class="widget-user-username">Elizabeth Pierce</h3>
            <h5 class="widget-user-desc">Web Designer</h5>
          </div>
          <div class="widget-user-image" style="margin-top:180px">
            <img class="img-circle" src="img/boy.png" alt="User Avatar" />
          </div>
          <div class="card-footer">
            <div class="row">
              <div class="col-sm-4 border-right">
                <div class="description-block">
                  <h5 class="description-header">3,200</h5>
                  <span class="description-text">SALES</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
              <div class="col-sm-4 border-right">
                <div class="description-block">
                  <h5 class="description-header">13,000</h5>
                  <span class="description-text">FOLLOWERS</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
              <div class="col-sm-4">
                <div class="description-block">
                  <h5 class="description-header">35</h5>
                  <span class="description-text">PRODUCTS</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
            </div>
            <!-- /.row -->
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <!-- /.col -->
      <div class="col-md-12">
        <div class="card">
          <div class="card-header p-2">
            <ul class="nav nav-pills">
              <li class="nav-item">
                <a class="nav-link" href="#activity" data-toggle="tab">Activity</a>
              </li>
              <li class="nav-item">
                <a class="nav-link active" href="#settings" data-toggle="tab">Settings</a>
              </li>
            </ul>
          </div>
          <!-- /.card-header -->
          <div class="card-body">
            <div class="tab-content">
              <div class="tab-pane" id="activity">
                <h2>No recent activities</h2>
              </div>

              <!-- /.tab-pane -->

              <div class="tab-pane active" id="settings">
                <form class="form-horizontal" @submit.prevent = "updateInfo">
                  <div class="form-group">
                    <label for="inputName"  class="col-sm-12 control-label">Name</label>

                    <div class="col-sm-10">
                      <input type="text" v-model="form.name"  class="form-control" id="inputName" placeholder="Name" />
                    </div>
                  </div>
                  <div class="form-group">
                    <label for="inputEmail" class="col-sm-12 control-label">Email</label>

                    <div class="col-sm-10">
                      <input type="email" v-model="form.email"  class="form-control" id="inputEmail" placeholder="Email" />
                    </div>
                  </div>

                  <div class="form-group">
                    <label for="inputExperience"   class="col-sm-12 control-label">Experience</label>

                    <div class="col-sm-10">
                      <textarea class="form-control" v-model="form.experience" id="inputExperience" placeholder="Experience"></textarea>
                    </div>
                  </div>

                  <div class="mb-3">
                    <label for="photo" class="col-sm-12 control-label">Profile Photo</label>

                    <div class="col-sm-10">
                      <input type="file" @change="updateProfile" name="photo" id="photo" />
                    </div>
                  </div>

                  <div class="form-group">
                    <label
                      for="inputSkills"
                      class="col-sm-12 control-label"
                    >Passport(Leave empty if not changing)</label>

                    <div class="col-sm-10">
                      <input type="text" class="form-control" id="inputSkills" placeholder="Skills" />
                    </div>
                  </div>

                  <div class="form-group">
                    <div class="col-sm-offset-2 col-sm-10">
                      <button type="submit" class="btn btn-primary">Submit</button>
                    </div>
                  </div>
                </form>
              </div>
              <!-- /.tab-pane -->
            </div>
            <!-- /.tab-content -->
          </div>
          <!-- /.card-body -->
        </div>
        <!-- /.nav-tabs-custom -->
      </div>
      <!-- /.col -->
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: new Form({
        id: "",
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    };
  },
  mounted() {
    console.log("Component mounted.");
  },
  methods:{


      updateProfile(e){
          let file = e.target.files[0];
          console.log(file)
          let reader= new FileReader();
          reader.onloadend = (file)=>{
              this.form.photo = reader.result
          }
          reader.readAsDataURL(file)
      },

    updateInfo(){
        this.form.put('api/profile/')
        .then(()=>{

        })
        .catch()
    }

  },
  created() {
    axios.get("api/profile").then(({ data }) => this.form.fill(data));
  }
};
</script>
