<template>
  <!--start email wrapper-->
  <div class="email-wrapper">
    <div class="email-sidebar">
      <div class="email-sidebar-header d-grid">
        <a href="javascript:;" class="btn btn-primary compose-mail-btn"
          ><i class="bx bx-plus me-2"></i> 上传</a
        >
      </div>
      <div class="email-sidebar-content">
        <div class="email-navigation">
          <div
            class="list-group list-group-flush"
            v-for="item in items"
            :key="item"
          >
            <a
              @click="gettablename(item)"
              class="list-group-item d-flex align-items-center"
              ><i class="bx bxs-file-blank me-3 font-20"></i
              ><span>{{ item }}</span></a
            >
          </div>
        </div>
      </div>
    </div>
    <div class="email-header d-xl-flex align-items-center">
      <div class="d-flex align-items-center">
        <div class="email-toggle-btn"><i class="bx bx-menu"></i></div>
        <div class="btn btn-white">
          <input class="form-check-input" type="checkbox" />
        </div>
        <div class="">
          <button @click="del()" type="button" class="btn btn-white ms-2">
            <i class="bx bx-trash me-0"></i>
          </button>
        </div>
      </div>
    </div>
    <div class="email-content">
      <div class="card">
        <div class="card-body">
          <div class="table-responsive">
            <table id="example2" class="table table-striped table-bordered">
              <thead>
                <tr>
                  <th v-for="heads in head" :key="heads">{{ heads }}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="indexs in message.index" :key="indexs">
                  <td v-for="td in message.td[indexs]" :key="td">{{ td }}</td>
                </tr>
              </tbody>
              <tfoot>
                <tr>
                  <th v-for="heads in head" :key="heads">{{ heads }}</th>
                </tr>
              </tfoot>
            </table>
          </div>
        </div>
      </div>
    </div>
    <!--start compose mail-->
    <div class="compose-mail-popup">
      <div class="card">
        <div class="card-header bg-dark text-white py-2 cursor-pointer">
          <div class="d-flex align-items-center">
            <div class="compose-mail-title">上传文件</div>
            <div class="compose-mail-close ms-auto">x</div>
          </div>
        </div>
        <div class="card-body">
          <div class="email-form">
            <form method="post" action="/index/" enctype="multipart/form-data">
              <div class="mb-3">
                <input type="text" name='table_name' class="form-control" placeholder="请输入你要保存的表名" />
              </div>
              <div class="row">
                <div class="col-xl-9 mx-auto">
                  <h6 class="mb-0 text-uppercase">Image Uploadify</h6>
                  <hr />
                  <div class="card">
                    <div class="card-body">
                        <input
                          id="image-uploadify"
                          type="file"
                          name='file'
                          accept=".xlsx,.xls,.csv"
                          multiple
                        />
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-md-12">
                <div class="col-md-6 col-md-offset-3">
                  <button type='submit'>提交</button>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
    <!--end compose mail-->
    <!--start email overlay-->
    <div class="overlay email-toggle-btn-mobile"></div>
    <!--end email overlay-->
  </div>
  <!--end email wrapper-->
</template>

<script>
import axios from "axios";
import $ from 'jquery'

export default {
  name: "index",
  inject:['reload'],
  data: function() {
    return {
      items: "",
      length: "",
      href: "",
      message: {
        td: "",
        index: "",
      },
      head: "",
      tablename: "",
    };
  },
  mounted: function() {
    axios({
      method: "GET",
      url: "/index/",
      data: {},
    }).then((res) => {
      console.log(res);
      var alltable = res.data.alltable;
      var l = alltable.length;
      var all_table = new Array();

      for (var i = 0; i < l; i++) {
        all_table[i] = alltable[i][0];
      }
      this.items = all_table;
      this.length = l;
    });
    try {
    $('#image-uploadify').imageuploadify();
    var table = $("#example2").DataTable({
      lengthChange: false,
      buttons: ["copy", "excel", "pdf", "print"],
    });
    table
      .buttons()
      .container()
      .appendTo("#example2_wrapper .col-md-6:eq(0)");
    }
    catch(err) {
  console.log(err.message);
      }

  },
  methods: {
    gettablename: function(arr) {
      this.tablename = arr
      axios({
        method: "POST",
        url: "/show",
        data: {
          arr: arr,
        },
      }).then((res) => {
        var headname = res.data.headname;
        var tablemessage = res.data.tablemessage;
        var l_tablemessage = tablemessage.length;
        var table_message = new Array();
        for (var h = 0; h < l_tablemessage; h++) {
          table_message[h] = h;
        }
        this.message.td = tablemessage;
        this.message.index = table_message;
        this.head = headname;
      });
    },
    del:function(){
    axios({
      method:'POST',
      url:'/dele/',
      data: {
        'option':this.tablename
      },
    }).then(function(arr){
      console.log(arr)
      location.reload();
    })

  },
},
  
};
</script>

<style scoped>

</style>
