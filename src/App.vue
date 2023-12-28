<script setup>
import {ref, onMounted} from 'vue';

const products = ref([]);
const currentIndex = ref(0);
const currentProductCategory = ref('');
const isAvailableCategory = ref(true);

// Fetch products from the API
onMounted(async () => {
  const response = await fetch('https://fakestoreapi.com/products');
  products.value = await response.json();
  console.log(products)
  updateCurrentProductCategory();
  updateAvailability();
});

const updateAvailability = () => {
  const currentCategory = products.value[currentIndex.value]?.category;

  isAvailableCategory.value = currentCategory == "men's clothing" || currentCategory == "women's clothing";
};

const getColor = (category) => {
  return category == "women's clothing" ? '#720060' :
      category == "men's clothing" ? '#002772' :
          '#DCDCDC';
}

const getBackgroundColor = (category) => {
  return category == "women's clothing" ? '#FDE2FF' :
      category == "men's clothing" ? '#D6E6FF' :
          '#DCDCDC';
}

const getNextButtonStyle = (category) => {
  return {
    border: `2px solid ${getColor(category)}`,
    color: getColor(category)
  };
};

const getRectangleStyle = (category) => {
  const pattern = './assets/bg-pattern.png'
  return {
    background: getBackgroundColor(category),
    backgroundImage: `url('${pattern}')`,
  }
}

const updateCurrentProductCategory = () => {
  currentProductCategory.value = products.value[currentIndex.value]?.category || '';
};

const nextProduct = () => {
  currentIndex.value = (currentIndex.value + 1) % products.value.length;
  updateCurrentProductCategory();
  updateAvailability();
};

const limitDescription = (description, wordLimit) => {
  const words = description.split(' ');
  if (words.length > wordLimit) {
    return words.slice(0, wordLimit).join(' ') + '...';
  }
  return description;
};
</script>

<template>
  <div class="container">
    <div class="rectangle" :style="getRectangleStyle(products[currentIndex]?.category)">
      <div v-if="isAvailableCategory">
        <div class="card">
          <div class="image-container" v-if="products.length > 0 && products[currentIndex].image">
            <img :src="products[currentIndex].image" alt="Woman Image" class="image">
          </div>
          <div class="product" v-if="products.length > 0">
            <!-- Menggunakan currentIndex untuk mengakses produk saat ini -->
            <div>
              <h1 class="product-name" :style="{ color: getColor(products[currentIndex].category) }">
                {{ products[currentIndex].title }}
              </h1>
              <div class="product-category">
                <p>{{ products[currentIndex].category }}</p>
                <div class="rating">
                  <p>{{ products[currentIndex].rating.rate }}/5</p>
                  <template v-for="i in 5">
                    <template v-if="i <= Math.floor(products[currentIndex].rating.rate)">
                      <!-- Ikon rating penuh -->
                      <svg width="15" height="15" viewBox="0 0 18 18" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="9" cy="9" r="9" :fill="getColor(products[currentIndex].category)" />
                      </svg>
                    </template>
                    <template v-else-if="i <= products[currentIndex].rating.rate">
                      <!-- Ikon rating setengah -->
                      <svg width="15" height="15" viewBox="0 0 18 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="9" cy="9" r="8.5" fill="white" :stroke="getColor(products[currentIndex].category)"/>
                      </svg>
                    </template>
                    <template v-else>
                      <!-- Ikon rating kosong -->
                      <svg width="15" height="15" viewBox="0 0 18 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle cx="9" cy="9" r="8.5" fill="white" :stroke="getColor(products[currentIndex].category)"/>
                      </svg>
                    </template>
                  </template>
                </div>
              </div>
              <hr>
            </div>
            <p class="product-desc">
              {{ limitDescription(products[currentIndex].description, 50) }}
            </p>
            <div>
              <hr>
              <h1 :style="{ color: getColor(products[currentIndex].category) }"
                  style="font-size: 28px; font-weight: bold">${{ products[currentIndex].price }}</h1>
              <div class="product-button">
                <div class="buy" :style="{ background: getColor(products[currentIndex].category) }">
                  Buy Now
                </div>
                <div class="next" @click="nextProduct" :style="getNextButtonStyle(products[currentIndex].category)">
                  Next Product
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <div class="card">
          <div class="sad">
            <div class="sad-content">
              <h3>This product is unavailable to show</h3>
              <div class="nextSad" @click="nextProduct" :style="{color: '#000000', 'border': '2px solid #000000'}">
                Next Product
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 100%;
  height: 100vh;
  background-color: white;
  position: relative;
}

.rectangle {
  width: 100%;
  height: 60%;
  background-position: center;
  background-repeat: no-repeat;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.card {
  width: 85%;
  height: 80vh;
  background: white;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  margin-top: 10%;
  padding: 50px;
  display: flex;
  border-radius: 10px;
  gap: 60px;
}

.image-container {
  width: 50%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.image {
  width: 100%;
  height: 100%;
}

.product {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.product-category {
  display: flex;
  justify-content: space-between;
  color: #3F3F3F;
  font-size: 16px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.product-button {
  display: flex;
  width: 100%;
  gap: 5px;
}

.product-name {
  font-size: 25px;
  line-height: inherit;
}

.product-desc {
  color: #1E1E1E;
  font-size: 16px;
  margin-top: -80px;
}

.buy {
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 2px;
  padding-bottom: 2px;
  width: 100%;
  color: white;
}

.next {
  border-radius: 4px;
  background: white;
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 2px;
  padding-bottom: 2px;
  width: 100%;
  cursor: pointer;
}

.rating {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 5px;
}

.sad {
  background-image: url("./assets/sad-face.svg");
  width: 100%;
  background-position: center;
  display: flex;
  justify-content: center;
  background-repeat: no-repeat;
  align-items: center;
}

.sad-content {
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 10px;
}

.nextSad {
  width: 130%;
  cursor: pointer;
}
</style>
