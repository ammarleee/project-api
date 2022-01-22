<template>
  <div>
    <v-dialog v-model="openDilog" width="500">
      <v-card class="pa-10">
        <v-card-title class="d-flex justify-content-center">
          <h2>إضافة مطعم</h2>
        </v-card-title>
        <v-form v-model="valid" @submit.prevent="register">
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-model="user.username"
                :rules="[allRules.required]"
                hint="مطلوب 6 أحرف على الاقل"
                dense
                outlined
                label="اسم المطعم"
                type="text"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-model="user.name"
                :rules="[allRules.required]"
                dense
                outlined
                label="اسم صاحب المطعم"
                type="text"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-model="user.phone"
                :rules="[allRules.required]"
                dense
                outlined
                label="رقم الهاتف"
                type="number"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-model="user.email"
                :rules="[allRules.required]"
                dense
                outlined
                label="البريد الإلكتروني"
                type="email"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-model="user.address"
                :rules="[allRules.required]"
                dense
                outlined
                label="عنوان المطعم"
                type="text"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-text-field
                v-if="!user._id"
                v-model="user.password"
                :rules="[allRules.required]"
                dense
                outlined
                label="كلمة السر"
                type="password"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row justify="center" aling="center">
            <v-col cols="12" md="8" class="d-flex justify-center">
              <v-btn type="submit" color="primary" :disabled="!valid" :loading="loading"
                >تسجيل الدخول</v-btn
              >
            </v-col>
          </v-row>
        </v-form>
      </v-card>
    </v-dialog>
    <div class="d-flex justify-content-right mt-10 mb-5">
      <v-btn class="success" @click="add">اضافة مطعم</v-btn>
    </div>
    <template v-if="data.length > 0">
      <v-card>
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="data" :search="search">
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon color="success" small @click="edit(item)">mdi-pen</v-icon>
            <v-icon color="error" small @click="del(item)">mdi-delete</v-icon>
          </template>

          <template v-slot:[`item.category`]="{ item }">
            <v-icon color="error" small @click="showcategory(item)">mdi-eye</v-icon>
          </template>

          <template v-slot:[`item.products`]="{ item }">
            <v-icon color="info" small @click="showproducts(item)">mdi-eye</v-icon>
          </template>
        </v-data-table>
      </v-card>
    </template>

    <v-dialog v-model="openDelete" width="500">
      <v-card class="pa-5">
        <h2 class="text-center">are you sure</h2>
        <v-card-actions class="d-flex justify-content-center">
          <v-btn class="success" @click="removeItem">sure</v-btn>
          <v-btn class="info" @click="openDelete = false">cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="openCategory" width="800">
      <v-card class="pa-5">
        <v-dialog v-model="addCategory" width="500">
          <v-card class="pa-5">
            <h2 class="text-center">إضافة صنف</h2>
            <v-form v-model="validTwo" @submit.prevent="addcategory">
              <v-row>
                <v-col cols="12" sm="12">
                  <v-text-field
                    v-model="mycategories.name"
                    :rules="[allRules.required]"
                    hint="مطلوب 6 أحرف على الاقل"
                    dense
                    outlined
                    label="اسم الصنف"
                    type="text"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="12">
                  <v-text-field
                    v-model="mycategories.description"
                    :rules="[allRules.required]"
                    hint="مطلوب 6 أحرف على الاقل"
                    dense
                    outlined
                    label="وصف الصنف"
                    type="text"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="12">
                  <div
                    v-if="mycategories.img"
                    class="d-flex justify-content-center position-relative"
                  >
                    <v-icon button @click="ref">mdi-camera</v-icon>
                    <img :src="mycategories.img" />
                  </div>
                  <v-text-field
                    v-else
                    @click="ref"
                    outlined
                    dense
                    placeholder="صورة الصنف"
                  ></v-text-field>
                  <input
                    class="d-none"
                    ref="uploadImg"
                    type="file"
                    :rules="[allRules.required]"
                    @change="uploadFile"
                    single
                  />
                </v-col>
              </v-row>
              <v-row justify="center" aling="center">
                <v-col cols="12" md="8" class="d-flex justify-center">
                  <v-btn type="submit" color="primary" :disabled="!validTwo" :loading="loading"
                    >تسجيل الاضافة</v-btn
                  >
                </v-col>
              </v-row>
            </v-form>
          </v-card>
        </v-dialog>
        <div class="d-flex justify-content-right mt-10 mb-5">
          <v-btn class="success" @click="openAddCategory">اضافة صنف</v-btn>
        </div>
        <template v-if="categoryData.length > 0">
          <v-card>
            <v-card-title>
              <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details
              ></v-text-field>
            </v-card-title>
            <v-data-table :headers="headersCategory" :items="categoryData" :search="search">
              <template v-slot:[`item.img`]="{ item }">
                <img width="100px" height="100px" :src="item.img" class="pa-2" />
              </template>
              <template v-slot:[`item.actions`]="{ item }">
                <v-icon color="success" small @click="editCategory(item)">mdi-pen</v-icon>
                <v-icon color="error" small @click="opendeleteCategory(item)">mdi-delete</v-icon>
              </template>
            </v-data-table>
            <v-dialog v-model="openDeleteCategory" width="500">
              <v-card class="pa-5">
                <h2 class="text-center">are you sure</h2>
                <v-card-actions class="d-flex justify-content-center">
                  <v-btn class="success" @click="sureDelCategory">sure</v-btn>
                  <v-btn class="error" @click="openDeleteCategory = false">cancel</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-card>
        </template>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      openDilog: false,
      openDelete: false,
      openCategory: false,
      addCategory: false,
      categoryData: [],
      mycategories: {},
      search: "",
      data: null,
      user: {},
      newuser: {},
      loading: false,
      valid: false,
      validTwo: false,
      deletedId: null,
      openDeleteCategory: false,
      deletedCategoryId: null,
      headers: [
        {
          text: "اسم المطعم",
          align: "center",
          filterable: false,
          value: "name"
        },
        {
          text: "العنوان",
          align: "center",
          value: "address"
        },
        {
          text: "رقم الهاتف",
          align: "center",
          value: "phone"
        },
        {
          text: "اصناف",
          align: "center",
          value: "category"
        },
        {
          text: "الاكلة",
          align: "center",
          value: "products"
        },
        {
          text: "تعديل او حذف",
          align: "center",
          value: "actions"
        }
      ],
      headersCategory: [
        {
          text: "الاسم",
          align: "center",
          value: "name"
        },
        {
          text: "الوصف",
          align: "center",
          filterable: false,
          value: "description"
        },
        {
          text: "الصورة",
          align: "center",
          value: "img"
        },

        {
          text: "تعديل او حذف",
          align: "center",
          value: "actions"
        }
      ],

      categories: [],
      products: [],
      images: null
    };
  },
  methods: {
    async register() {
      if (this.user._id) {
        this.loading = true;
        try {
          const res = await axios.put(
            "https://resturant-appv1.herokuapp.com/user/edit-profile",
            this.user
          );
          console.log(res);
          let itemEdit = res.data.user;

          let index = this.data.findIndex(i => {
            return i._id === itemEdit._id;
          });
          console.log(index);
          this.data.splice(index, 1, itemEdit);

          this.loading = false;
        } catch (err) {
          console.log(err);
          this.loading = false;
        }

        this.openDilog = false;
      } else {
        this.loading = true;
        try {
          const res = await axios.post(
            "https://resturant-appv1.herokuapp.com/user/signup",
            this.user
          );
          console.log(this.user);
          this.data.push(res.data.user);
          console.log(res.data.user);
          this.loading = false;
        } catch (err) {
          console.log(err);
          this.loading = false;
        }
        this.openDilog = false;
      }
    },
    openAddCategory() {
      this.addCategory = true;
      this.mycategories = {};
    },
    uploadFile(e) {
      this.images = e.target.files;
    },
    async addcategory() {
      if (this.mycategories.resturantId) {
        this.loading = true;

        const formData = new FormData();
  
        formData.append("files", this.images[0]);
        formData.append("name", this.mycategories.name);
        formData.append("resturantId", JSON.stringify(this.mycategories.resturantId));

        try {
          const res = await axios.post(
            "https://resturant-appv1.herokuapp.com/category-edit",
            formData
          );
          console.log(res);
          let itemEdit = res.data.category;

          let index = this.categoryData.findIndex(i => {
            return i._id === itemEdit._id;
          });
          console.log(index);
          this.categoryData.splice(index, 1, itemEdit);

          this.addCategory = false;
        } catch (err) {
          console.log(err);
          this.addCategory = false;
        }
        this.loading = true;
      } else {
        this.addCategory = true;
        try {
           const formData = new FormData();
  
        formData.append("files", this.images[0]);
        formData.append("name", this.mycategories.name);
        formData.append("resturantId", this.mycategories.resturantId);
console.log(this.mycategories);
console.log(this.images);
          const res = await axios.post(
            "https://resturant-appv1.herokuapp.com/add-category",
            formData
          );
          console.log(res);
          console.log(this.mycategories);
          // this.categoryData.push(res.data.mycategories);
          // console.log(res.data.mycategories);
          this.loading = false;
        } catch (err) {
          console.log(err);
          this.loading = false;
        }
        this.addCategory = false;
      }
      this.loading = false;
    },
    editCategory(item) {
      this.addCategory = true;
      this.mycategories = { ...item };
    },
    opendeleteCategory(item) {
      this.openDeleteCategory = true;
      this.deletedCategoryId = item._id;
    },
    async sureDelCategory() {
      try {
        this.loading = true;
        const res = await axios.delete(
          `https://resturant-appv1.herokuapp.com/user/${this.deletedCategoryId}`
        );
        console.log(res);
        this.openDeleteCategory = false;
        this.loading = true;
        this.categoryData = this.categoryData.filter(e => {
          console.log(e);
          return e._id !== this.deletedCategoryId;
        });
      } catch (err) {
        console.log(err);
        this.openDeleteCategory = false;
        this.loading = true;
      }
      this.loading = false;
    },
    ref() {
      this.$refs.uploadImg.click();
    },

    async showcategory(item) {
      this.openCategory = true;
      this.mycategories = {};
      try {
        const res = await axios.get(
          `https://resturant-appv1.herokuapp.com/resturant/categories/${item._id}`
        );
        console.log(res);
        this.categoryData = res.data.categoryies;
        console.log(this.categoryData);
      } catch (error) {
        console.log(error);
      }
    },
    async showproducts(item) {
      try {
        const res = await axios.get(
          `https://resturant-appv1.herokuapp.com/resturant/products/${item._id}`
        );
        console.log(res);
      } catch (error) {
        console.log(error);
      }
    },
    add() {
      this.openDilog = true;
      this.user = {};
    },
    edit(item) {
      this.openDilog = true;
      this.user = { ...item };
    },
    del(item) {
      this.openDelete = true;
      this.deletedId = item._id;
    },
    async removeItem() {
      try {
        this.loading = true;
        const res = await axios.delete(
          `https://resturant-appv1.herokuapp.com/user/${this.deletedId}`
        );
        console.log(res);
        this.openDelete = false;
        this.loading = true;
        this.data = this.data.filter(e => {
          console.log(e);
          return e._id !== this.deletedId;
        });
      } catch (err) {
        console.log(err);
        this.openDelete = false;
        this.loading = true;
      }
    }
  },
  async mounted() {
    try {
      const res = await axios.get("https://resturant-appv1.herokuapp.com/resturants");
      console.log(res.data.resturants);
      this.data = res.data.resturants;
    } catch (err) {
      console.log(err);
    }
  }
};
</script>

<style lang="scss" scoped></style>
