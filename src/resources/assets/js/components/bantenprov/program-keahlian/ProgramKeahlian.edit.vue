<template>
  <div class="card">
    <div class="card-header">
      <i class="fa fa-table" aria-hidden="true"></i> Edit Program Keahlian

      <ul class="nav nav-pills card-header-pills pull-right">
        <li class="nav-item">
          <button class="btn btn-primary btn-sm" role="button" @click="back">
            <i class="fa fa-arrow-left" aria-hidden="true"></i>
          </button>
        </li>
      </ul>
    </div>

    <div class="card-body">
       <vue-form class="form-horizontal form-validation" :state="state" @submit.prevent="onSubmit">

        <validate tag="div">
          <div class="form-group">
            <label for="label">Label</label>
            <input type="text" class="form-control" id="label" v-model="model.label" name="label" placeholder="Label" required autofocus>
            <field-messages name="label" show="$invalid && $submitted" class="text-danger">
              <small class="form-text text-success">Looks good!</small>
              <small class="form-text text-danger" slot="required">This field is a required field</small>
            </field-messages>
          </div>
        </validate>

        <validate tag="div">
          <div class="form-group">
            <label for="keterangan">Keterangan</label>
            <input type="text" class="form-control" id="keterangan" v-model="model.keterangan" name="keterangan" placeholder="keterangan" required>
            <field-messages name="keterangan" show="$invalid && $submitted" class="text-danger">
              <small class="form-text text-success">Looks good!</small>
              <small class="form-text text-danger" slot="required">This field is a required field</small>
            </field-messages>
          </div>
        </validate>

        <div class="form-row mt-4">
          <div class="col-md">
            <validate tag="div">
            <label for="user_id">Username</label>
            <v-select name="user_id" v-model="model.user" :options="user" class="mb-4"></v-select>

            <field-messages name="user_id" show="$invalid && $submitted" class="text-danger">
              <small class="form-text text-success">Looks good!</small>
              <small class="form-text text-danger" slot="required">Username is a required field</small>
            </field-messages>
            </validate>
          </div>
        </div>

        <div class="form-group">
          <button type="submit" class="btn btn-primary">Submit</button>
          <button type="reset" class="btn btn-secondary" @click="reset">Reset</button>
        </div>

      </vue-form>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    axios.get('api/program-keahlian/' + this.$route.params.id + '/edit')
      .then(response => {
        if (response.data.loaded == true) {

          this.model.keterangan     = response.data.program_keahlian.keterangan;
          this.model.label          = response.data.program_keahlian.label;
          this.model.old_user_id    = response.data.program_keahlian.user_id;
          this.model.user           = response.data.user;
          this.model.old_user       = response.data.program_keahlian.user;
          

        } else {
          alert('Failed');
        }
      })
      .catch(function(response) {
        alert('Break');
        window.location.href = '#/admin/program-keahlian';
      });

      axios.get('api/program-keahlian/create')
      .then(response => {           
          response.data.user.forEach(element => {
            this.user.push(element);
          });

      })
      .catch(function(response) {
        alert('Break');
      })
  },
  data() {
    return {
      state: {},
      model: {
        keterangan:          "",
        label:               "",
        user:                "",
        
      },
      user: []
    }
  },
  methods: {
    onSubmit: function() {
      let app = this;

      if (this.state.$invalid) {
        return;
      } else {
        axios.put('api/program-keahlian/' + this.$route.params.id, {
            user_id:            this.model.user.id,
            old_user_id:        this.model.old_user_id,
            keterangan:         this.model.keterangan,
            label:              this.model.label,
          })
          .then(response => {
            if (response.data.loaded == true) {
              if(response.data.message == 'success'){
                alert(response.data.message);
                app.back();
              }else{
                alert(response.data.message);
              }
            } else {
              alert(response.data.message);
            }
          })
          .catch(function(response) {
            alert('Break ' + response.data.message);
          });
      }
    },
    reset() {
      axios.get('api/program-keahlian/' + this.$route.params.id + '/edit')
        .then(response => {
          if (response.data.loaded == true) {
            this.model.user           = response.data.program_keahlian.user;
            this.model.keterangan     = response.data.program_keahlian.keterangan;
            this.model.label          = response.data.program_keahlian.label;
          } else {
            alert('Failed');
          }
        })
        .catch(function(response) {
          alert('Break ');
        });
    },
    back() {
      window.location = '#/admin/program-keahlian';
    }
  }
}
</script>
