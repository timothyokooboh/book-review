<template>
  <div>
    <Splash-Screen />
    <div class="error" v-if="error">
      Couldn't load data. Try reloading the page.
    </div>
    <section class="books" v-if="success">
      <div
        class="book"
      >
        <div class="search">
          <div class="search__field">
            <input type="text" placeholder="Enter ISBN" v-model="isbn">
          </div>
          <div> <button @click="getBook">Submit</button> </div>
        </div>
        <div v-if="loading" class="loading"> Loading data...</div>
         <h1>{{book.volumeInfo.title}}</h1>

        <div class="book__details">
          <div>
            <div>
              <img :src="book.volumeInfo.imageLinks.thumbnail" class="book__img">
            </div>
            <div>
              <div class="book__author">{{book.volumeInfo.authors.join(", ")}}</div>
              <div class="book__rating">  
                <span> {{book.volumeInfo.averageRating}} </span>
                <span v-color="'#EF3064'"> &#9734; </span>
              </div>
              <div class="book__reviews-count">{{book.volumeInfo.ratingsCount}} reviews</div>
            </div>
          </div>

          <div>
            <div>Category: {{book.volumeInfo.categories[0]}}</div>
            <div>PDF: {{book.accessInfo.pdf.isAvailable | yesOrNo }}</div>
            <div>EPUB: {{book.accessInfo.epub.isAvailable | yesOrNo }}</div>
          </div>
        </div>

        <div class="book__description">{{book.volumeInfo.description}}</div>

        <div class="book__more-details">
          <div>
            <div class="book__page-count">Page count: {{book.volumeInfo.pageCount}}</div>
            <div class="book__preview-link">
              <a :href="book.volumeInfo.previewLink" target="_blank">Preview book</a>
            </div>
          </div>

          <div>
            <div class="book__published-date">Published date: {{book.volumeInfo.publishedDate}}</div>
            <div class="book__publisher">Publisher: {{book.volumeInfo.publisher}}</div>
          </div>
        </div>

      </div>
    </section>
  </div>
</template>

<script>
// Using axios for sending HTTP requests
import axios from "axios";

// Slash screen component
import SplashScreen from "./components/SplashScreen";

export default {
  components: {
    SplashScreen
  },
  data() {
    return {
      book: "",
      isbn: "",
      error: true,
      success: false,
      loading: true
    }
  },
  filters: {
    yesOrNo(value) {
      return value == true ? "YES" : "NO"
    }
  },
  directives: {
    color: {
      bind(el, binding) {
        el.style.color = binding.value
      }
    }
  },
  methods: {
    async getBook() {
      this.loading = true;
      try {
      const books = await axios.get(`https://www.googleapis.com/books/v1/volumes?q=isbn:${this.isbn}`)
      console.log(books.data.items[0])
      this.book = books.data.items[0]
      this.loading = false;
    }
    catch(err) {
      //this.error = true;
      alert("Couldn't fetch data. Please try again.");
      this.loading = false;
      console.log(err)
    }
    }
  },

  async created() {
    try {
      const books = await axios.get(`https://www.googleapis.com/books/v1/volumes?q=isbn:0747532699`)
      console.log(books.data.items[0])
      this.book = books.data.items[0]
      this.success = true;
      this.error = false;
      this.loading = false;
    }
    catch(err) {
      this.error = true
      this.loading = false;
      console.log(err)
    }
  }
}
</script>

<style lang="scss">

  @mixin boxShadow {
    box-shadow: 0 1rem 1.5rem rgba(0, 0, 0, .15);
  }

  $primary-color:  #EE2F63;

  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

  html {
    font-size: 62.5%; // 1rem = 10px
  }

  body {
    box-sizing: border-box;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

  .books {
    background-image: linear-gradient(to right bottom, #eb2f64, #FF3366);
    min-height: 100vh;
    padding: 3rem;
    display: flex;
    align-items: center;
    justify-content: center;

    @media only screen and (max-width: 21.5em) {
      padding: 0;
    }
  }

  .book {
    background-color: #FFF;
    color: #333;
    padding: 2rem 2rem 3rem;
    border-radius: 4px;
    @include boxShadow;

    @media only screen and (max-width: 24.38em) {
      width: 100%;
    }

    h1 {
      text-align: center;
      font-size: 2.5rem;
      font-weight: 300;
      line-height: 4rem;
    }

    &__img {
      height: 20rem;
      width: 15rem;
      display: block;
      margin-right: 2rem;

      @media only screen and (max-width: 46.875em) {
        height: 20rem;
        width: 12rem;
      }
    }

    &__author {
      font-size: 2rem;
    }

    &__details {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;
      @include boxShadow;
      align-items: flex-start;
      margin-top: 3rem;
      padding: 2rem;
      border-radius: 5px;

      @media only screen and (max-width: 46.875em) {
        display: block;
      }

      @media only screen and (max-width: 24.38em) {
        box-shadow: none;
      }

      & > * {
        margin-top: 2rem;
      }

      & > :first-child {
        display: flex;
        padding-right: 2rem;
      }

      & > :nth-child(2) {
        font-size: 1.4rem;

        & > * {
          padding: 1rem 0;
        }
      }
    }

    &__rating {
      padding: 1rem 0;
      font-size: 1.4rem;
    }

    &__reviews-count {
      font-size: 1.4rem;
    }

    &__description {
      margin: 4rem 0 4rem;
      font-size: 1.5rem;
      letter-spacing: 1.08px;
      line-height: 3rem;
      column-count: 3;
      column-gap: 4rem;
      column-rule: 2px solid #FDCC18;

      @media only screen and (max-width: 46.875em) {
        column-count: 2;
        margin: 2rem 0 4rem;
      }

      @media only screen and (max-width: 33.125em) {
        column-count: 1;
        hyphens: auto;
      }
    }

    &__more-details {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;

      @media only screen and (max-width: 37.5em) {
        display: block;

        & > :first-child {
          display: flex;
          flex-wrap: wrap;
          justify-content: space-between;
          align-items: baseline;
        }
      }

      & > * {
        border-top: 2px solid #eb2f64;
        padding-top: 1rem;

        @media only screen and (max-width: 37.5em) {
          margin-bottom: 2rem;
        }
        
      }
    }

    &__published-date, &__page-count {
      font-size: 1.4rem;
      padding-bottom: 1rem;
    }

    &__publisher, &__preview-link {
      font-size: 1.4rem;
    }

    &__preview-link {
        margin-top: 1.5rem;
        font-size: 1.2rem;
        border: 1px solid #EE2F63;
        padding: .5rem 1rem;
        border-radius: 4px;

        @media only screen and (max-width: 37.5em) {
          display: inline-block;
        }
        
      a:link,
      a:visited {
        color: $primary-color;
        text-decoration: none;
        text-transform: uppercase;
      }
    }

  }

  .search {
    max-width: 50rem;
    width: 90%;
    margin: 2rem auto 3rem;
    display: flex;

    &__field {
      width: 100%;
    }

    button {
      color: #fff;
      background-color: dodgerblue;
      border: none;
      outline: none;
      padding: .5rem 1rem;
      letter-spacing: 1.08px;
      margin-left: -7rem;
      margin-top: 4px;
      border-radius: 10rem;
      cursor: pointer;
    }

    input {
      width: 100%;
      height: 4rem;
      outline: none;
      border: none;
      @include boxShadow;
      padding: .5rem 1rem;
      border-radius: 10rem;
    }
  }

  .error {
    font-size: 2rem;
    color: $primary-color;
    display: flex;
    height: 100vh;
    align-items: center;
    justify-content: center;
    padding: 0 1rem;
  }

  .loading {
    text-align: center;
    font-size: 2rem;
    padding: 2rem 0;
  }
</style>