<template>
  <modal
    v-bind:is-showing="true"
    title="Edit Product"
    success-button="Save"
    v-on:success="success"
    v-on:cancel="cancel"
  >
    <form v-on:submit.prevent="onSubmit">
      <p v-if="error" class="is-danger">{{ error }}</p>
      <div class="field">
        <label class="label">Product Name:</label>
        <div class="control">
          <input class="input" type="text" placeholder="Product name" v-model="tempProduct.name">
        </div>
      </div>
      <div class="field">
        <label class="label">Description:</label>
        <div class="control">
          <textarea
            class="input textarea"
            type="textarea"
            placeholder="Product description"
            v-model="tempProduct.description"
          ></textarea>
        </div>
      </div>
      <div class="field">
        <label class="label">Price:</label>
        <div class="control">
          <input class="input" type="text" placeholder="Price" v-model="tempProduct.price">
        </div>
      </div>
      <div class="field">
        <label class="label">Image Url(s):</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Product image url"
            v-model="tempProduct.imageUrls"
          >
        </div>
      </div>
      <span>
        <img id="product-preview-image" :src="tempProduct.imageUrls">
      </span>
      <div class="field">
        <label class="label">Stock/Inventory Count:</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Stock/inventory count"
            v-model="tempProduct.stockCount"
          >
        </div>
      </div>
      <div class="field">
        <label class="label">Item Categories:Tags: (separated by ";")</label>
        <label class="label">ex: brand:Willier;color:Orange;size:Medium;type:Road</label>
        <div class="control">
          <input
            class="input"
            type="text"
            placeholder="Product categories/tags"
            v-model="tempProduct.tagString"
          >
        </div>
      </div>
      <label class="checkbox">
        <input type="checkbox" v-model="tempProduct.inStoreOnly">
        In Store Only
      </label>
    </form>
  </modal>
</template>

<script lang="ts">
import axios, { AxiosResponse } from "axios";
import { APIConfig } from "../utils/api.utils";
import { Component, Prop, Vue } from "vue-property-decorator";
import Modal from "./Modal.vue";
import { iProduct } from "../models/product.interface";

@Component({
  components: { Modal }
})
export default class EditProduct extends Vue {
  // @Prop(Boolean) isShowing: boolean = false;
  @Prop() product!: iProduct;
  public tempProduct: iProduct = {
    id: this.product.id,
    name: this.product.name,
    description: this.product.description,
    price: this.product.price,
    imageUrls: this.product.imageUrls,
    stockCount: this.product.stockCount,
    tagString: this.product.tagString,
    tags: this.product.tags,
    inStoreOnly: this.product.inStoreOnly
  };
  error: string | boolean = false;

  success() {
    this.error = false;
    axios
      .put(APIConfig.buildUrl(`/products/${this.product.id}`), this.tempProduct, {
        headers: {
          token: this.$store.state.userToken
        }
      })
      .then((response: AxiosResponse<iProduct>) => {
        this.$emit("success");
      })
      .catch((reason: any) => {
        this.error = reason.message;
      });
  }
  cancel() {
    this.$emit("cancel");
  }

  mounted() {
    
  }

}
</script>

<style lang="scss">
#product-preview-image {
  height: 100px;
}
</style>