<template>
<div class="container-edit-main flex-col center">
    <!-- <form @submit.prevent="editProduct"> -->
    <h1>Edit Product</h1>

  <div class="container-edit-form flex-col">
    <!-- <label for="name">Product Name</label> -->
    <vs-input label="Product Name" type="text" v-model="name" name="name" />
    <label for="description">Description</label>
    <textarea type="text" v-model="description" label="Description" name="description" cols="15" rows="5"></textarea>
    <!-- <label for="price">Price</label> -->
    <vs-input label="Price" type="number" v-model="price" name="price"/>
    <!-- <label for="stock">Stock</label> -->
    <vs-input label="Stock" type="number" v-model="stock" name="stock"/>
    <div class="flex-row fullwidth">
      <vs-input label="Image URL" type="text" v-model="imageUrl" name="imageUrl"/>
      <vs-button class="btn" succes @click="previewUrl = imageUrl">Preview</vs-button>
    </div>
      <img class="image-preview" :src="previewUrl" alt="">
    <label for="categories">Category</label>
    <select filter name="categories" label="Category" v-model="ProductCategoryId">
      <option v-for="cat in categories" :key="cat.id" :value="cat.id">{{cat.name}}</option>
    </select>

    <div class="flex-row center fullwidth" >
      <vs-button flat dark @click.prevent="$router.go(-1)">Cancel</vs-button>
      <vs-button block @click="editProduct">Update</vs-button>
    </div>
    <!-- </form> -->
  </div>
  </div>
</template>

<script>
export default {
  name: 'ProductEdit',
  computed: {
    categories () {
      return this.$store.state.categories
    }
  },
  data () {
    return {
      name: '',
      description: '',
      price: 0,
      stock: 0,
      imageUrl: '',
      ProductCategoryId: '',
      previewUrl: ''
    }
  },
  created () {
    this.$store.dispatch('fetchProductDetails', this.$route.params.id)
      .then(({ data }) => {
        console.log(data, '<<<<< product details')
        this.name = data.name
        this.description = data.description
        this.price = data.price
        this.stock = data.stock
        this.imageUrl = data.imageUrl
        this.previewUrl = data.imageUrl
        this.ProductCategoryId = data.ProductCategoryId
      })
      .catch(err => {
        console.log(err.response)
        this.$toasted.global.errorMessage(err.response.data.msg)
      })
  },

  methods: {
    editProduct () {
      console.log('submit edit product')
      this.$store.dispatch('editProduct', {
        id: this.$route.params.id,
        name: this.name,
        description: this.description,
        price: this.price,
        stock: this.stock,
        imageUrl: this.imageUrl,
        ProductCategoryId: this.ProductCategoryId
      })
    }
  }

}
</script>

<style>

  .container-edit-form {
    width: 60%;
    min-width: 400px;
    justify-content: flex-start;
    align-items: flex-start;
  }
/* form {
  display: flex;
  flex-direction: column
} */
  .container-edit-form .vs-input-parent {
    /* min-width: 300px */
    margin-bottom: 2rem;
    width: 100%;
  }

  .image-preview {
    width:300px;
    height: auto;
    margin-bottom: 1.5rem;
  }

  .btn {
    height: 32px;
  }

  .vs-input-parent {
    align-self: flex-start !important
  }

  .vs-input-parent[name=price], .vs-input-parent[name=stock] {
    width: 150px !important
  }

  .vs-input, textarea {
    width: 100% !important
  }

  label, vs-input__label {
    text-align: left;
    font-size: 0.75rem
  }
  textarea, select {
    border-radius: 12px;
    background-color: rgb(244, 247, 248);
    /* background-color: rgb(192, 144, 231); */
    border: 0 ;
    padding: 7px 13px 7px 10px;
    outline: none;
    margin-bottom: 2rem;
  }

</style>
