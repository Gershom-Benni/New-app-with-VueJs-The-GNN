<script setup>
import { ref, onMounted, computed, watch } from 'vue';
const API_KEY = '15f9a3e22849470ba4b802583df89e82';

const articles = ref([]);
const category = ref('all');
const nav_items = ['all','business', 'entertainment', 'general', 'health', 'science', 'sports', 'technology'];

const getNewsUrl = (category) => {
  if (category === 'all') {
    return `https://newsapi.org/v2/everything?q=apple&from=2024-07-29&to=2024-07-29&sortBy=popularity&apiKey=${API_KEY}`;
  }
  return `https://newsapi.org/v2/top-headlines?category=${category}&language=en&apiKey=${API_KEY}`;
};

const fetchNews = async (category) => {
  const endpoint = getNewsUrl(category);
  console.log(endpoint)
  try {
    const response = await fetch(endpoint);
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    articles.value = data.articles || [];
  } catch (error) {
    console.error('There was a problem with the fetch operation:', error);
  }
};

const filteredArticles = computed(() => {
  return articles.value.filter(article => 
    article.urlToImage && article.urlToImage.trim() !== '' && article.title && article.description
  );
});

const truncateText = (text, maxLength) => {
  if (!text) return '';
  return text.length > maxLength ? text.substring(0, maxLength) + '...' : text;
};
const updateCategory = (newCategory) => {
  category.value = newCategory;
  fetchNews(newCategory);
};


onMounted(() => {
  fetchNews(category.value);
});

</script>



<template>

<!-- Nav BAR -->
<nav>
    <div class="brand">
      <h1 class="title">The GNN</h1>
      <p>General News Network</p>
      <!-- <img src="/public/logo.png" class="logo"> -->
    </div>
    <ul>
      <li v-for="item in nav_items" :key="item">
        <button @click="updateCategory(item)" type="button" class="btn btn-outline-info btn-sm">
            {{ item.charAt(0).toUpperCase() + item.slice(1) }}
        </button>

      </li>
    </ul>
  </nav>
  <div class="line"></div>


 <div class="news-container">
    <div class="card-container">
      <div v-for="article in filteredArticles" :key="article.title" class="card">
        <img :src="article.urlToImage" alt="Article Image" class="card-image" />
        <div class="card-content">
          <h2 class="card-title">{{ truncateText(article.title, 50) }}</h2>
          <p class="card-description">{{ truncateText(article.description, 100) }}</p>
          <a :href="article.url" target="_blank" class="read-more-btn"><button type="button" class="btn btn-danger">Read More...</button></a>
        </div>
      </div>
    </div>
    <p v-if="!filteredArticles.length">Loading...</p>
  </div>
</template>

<style scoped>
.logo{
  width: 100px;
}
.news-container {
  padding: 20px;
  width: 80vw;
  margin: 0 auto;
}

.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content:center ;
  gap: 20px;
}

.card {
    background: rgba( 255, 255, 255, 0.25 );
    box-shadow: 0 8px 32px 0 rgba( 31, 38, 135, 0.37 );
    backdrop-filter: blur( 4px );
    -webkit-backdrop-filter: blur( 4px );
    border-radius: 10px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    width: 100%;
    max-width: 280px;
    padding: 8px;
    display: flexbox;
    justify-content:space-between 
}

.card-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 10px;
}

.card-content {
    /* height: 100%; */
  padding: 15px;
  color: white;
  display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.card-title {
    font-family: "Poppins", sans-serif;
    font-size: 1.2em;
    margin: 0;
}

.card-description {
  font-size: 0.8em;
  color: #c1c1c1;
}

.read-more-btn {
    align-self: center;
  text-decoration: none;
}

.read-more-btn:hover {
  text-decoration: underline;
}


/* Nav bar styles */
nav{
      display: flex;
      justify-content: space-between;
    }
    nav ul{
      list-style: none;
      display: flex;
      justify-content: space-between;
      gap: 10px;
      max-width: 50%;
      margin-right: 25px;
      margin-top: 35px;
    }
    nav ul li{
      color: white;
      font-size: 20px;
    }
    .brand{
      /* margin-left: 25px; */
      margin-top: 15px;
      text-align: center;
      width: 20%;
      /* margin-bottom: 20px */
    }
    .brand p{
      color: white;
      font-size: 10px;
    }
    .title {
        font-family: "Bodoni Moda SC", serif;
        font-optical-sizing: auto;
        font-weight: 600;
        font-style: normal;
        color: white;
        margin: 0;
      }

    .line{
      background: rgba(255, 255, 255, 0.255);
      width: 100%;
      height: 1px;
    }
</style>
