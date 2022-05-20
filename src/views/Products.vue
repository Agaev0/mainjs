 
<template>
  <div>
    <select class="sorting" name="sort" v-model="sort" @change="asd($event)">
      <option value="updatedAt:desc">Newest</option>
      <option value="price:asc">Price: Ascending</option>
      <option value="price:desc">Price: Descending</option>
    </select>
    <div class="flex-left">

     <svg   xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
  <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
</svg><input  class="searh" type="text" v-model="searchString" @input="pushQueryToUrl({search: searchString})"/>

 </div>
    <div class="proo">
    <h1 class="page">Products</h1>
  </div>
    <div class="categoriyy">
    <div v-for="c in categories.data" :key="c.id">
   
    
    <button class="gradient-button" @click="pushQueryToUrl({categoryId: c.id})">
    <div class="tutl">{{ c.title }}</div></button>
    </div>
    </div>
    <div>

    </div>
     

    <div class="flex-product">
    <div class="size" v-for="p in products.data" :key="p.id">
      <p>{{ p.title }}</p>
      <img src="https://object.pscloud.io/cms/cms/Photo/img_0_77_2661_0_6.png">
      <p>{{ p.price }} $ </p>
      <!-- <p>{{ p.spec }}</p> -->
      </div>
       
    </div>

    <div class="ok">
    <div class="olk" v-for="i in products?.meta?.pagination?.pageCount" :key="i">
    <button @click="pushQueryToUrl({page: i})">{{ i }}</button>
    </div>
  <div>
</div>
      <div class="truj">
       <input type="checkbox"   value='wifi:5' @change="pushQueryToUrl({spec: $event.target.value})">wifi 5
       <input type="checkbox"   value='let:true' @change="pushQueryToUrl({spec: $event.target.value})">true
       <input type="checkbox"   value='Lidar:true' @change="pushQueryToUrl({spec: $event.target.value})">lidar
      </div>
    </div>
  </div>
   

</template>
<script>
 

import { useECommerceStore } from "@/stores/e-commerce";
import { useRoute, useRouter } from "vue-router";
import { ref, onMounted, watchEffect } from "vue";
import { storeToRefs } from 'pinia'
import qs from 'qs'
export default {
  setup() {
    const eCommerceStore = useECommerceStore();
    const { products } = storeToRefs(eCommerceStore);
    const { categories } = storeToRefs(eCommerceStore);
    const route = useRoute();
    const router = useRouter();
    const sort = ref(route.query.sort || "updatedAt:desc");
    const searchString = ref('')
    const spec = ref()
    let ezQuery = {};
    function asd(event) {
      pushQueryToUrl({ sort: event.target.value });
    }
    function pushQueryToUrl(queryParam) {
      Object.entries(queryParam).forEach(([key, value]) => {
        if (value) {
          ezQuery[key] = value;

        }else{
          ezQuery[key] = undefined;
        }
      });
      
      router.push({query: ezQuery})
    }
    async function createProductQuery() {
      ezQuery = {
        page: route.query.page,
        gte: route.query.gte,
        lte: route.query.lte,
        sort: route.query.sort,
        categoryId: route.query.categoryId,
        spec: route.query.spec ? route.query.spec.split(',').map(s => s.split(':')) : undefined,
        search: route.query.search != '' || route.query.search ? route.query.search : undefined
      }
      console.log(ezQuery.spec)
      const pagination = {page: route.query.page || 1, pageSize: 5};
      const populate = ["category"];
      const sort = route.query.sort ? [route.query.sort] : ["updatedAt:desc"];
      const search = route.query.search != '' && route.query.search ? route.query.search : undefined
      const spec = route.query.spec
      const filters = {
          $and: [
            {
              price: {
                $gte: route.query.price_gte,
              },
            },
            {
              price: {
                $lte: route.query.price_lte,
              },
            },
            {
              category:{
                id: {
                  $eq: route.query.categoryId || categories[0]?.id,
                }
              }
            },
            {
              title: {
                $startsWith: search,
              }
            },
          ],
        };
      if (ezQuery.spec) {
        ezQuery.spec.map(s => {
          filters.$and.push({
            spec: {
              $containsi: `"${s[0]}":${s[1]}`
            }
          })
        })
      }
     
      
      const query = {
        populate,
        pagination,
        sort,
        filters,
      }
      await eCommerceStore.fetchCategories();
      await eCommerceStore.fetchProducts(query);
      console.log(filters);
    }
    watchEffect(() => {      
      createProductQuery();
    });
    onMounted(() => {});    
    return {
      products,
      eCommerceStore,
      pushQueryToUrl,
      categories,
      sort,
      asd,
      searchString
    };
  },
  name: "Products",
};
</script>
 

<style scoped>

.gradient-button {
  text-decoration: none;
  display: inline-block;
  color: white;
  padding: 5px 30px;
  margin: 10px 20px;
  font-family: 'Montserrat', sans-serif;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-image: linear-gradient(to right, rgb(75, 116, 109) 0%, rgba(60, 47, 145, 0.781) 51%, rgb(239, 204, 158) 100%);
  background-size: 200% auto;
  box-shadow: 0 0 20px rgba(0, 0, 0, .1);
  transition: .5s;
}
.gradient-button:hover {
  background-position: right center;
}
 .flex-left{display: flex; justify-content: flex-end}
 svg{height: 25px;}
 
.sorting {
    padding: 5px 10px;
    /* border-radius: 10px; */
    /* background-color: rgb(199, 194, 194); */
    text-align: center;
}

  .flex-product{
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
    align-content: center;
}

  .size { 
  
  
    border: solid #8080800a;
    border-radius: 20px;
    transition: .8s;
    }

  .size:hover{ 
    box-shadow: 20px 10px 20px gray; 
    }
  
  img { 
    height: 170px;
    }

 .page {
    text-align: center;
    font-family: monospace;
    color: #1b0624;
 }
  
 .proo {
    background-color: #008b6bad;
 }

 .truj {
    padding: 30px;
 }

 .ok {
    display: flex;
    align-items: center;
    margin: 30px;
 }

 .categoriyy {
    display: flex;
    justify-content: center;
 }


</style>
