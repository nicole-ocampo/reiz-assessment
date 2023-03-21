<template>
  <div class="hello">
    <div id="header">
      <h1>{{ header }}</h1>
      <p>
        This is part of the homework assignment for the <b>front-end developer internship</b> position. <br>
        Built with <b>VueJs</b>. See more of my works <a href="https://nicoleocampo.com" target="_blank" rel="noopener">here</a>.
      </p>
    </div>

    <div id="options-wrap">
      <div id="filter-button-wrap">
        <p><b>Filter options: </b></p>
        <div class="filter-button" @click="filter_all">All</div>
        <div class="filter-button" @click="filter_lithuania">Smaller than Lithuania</div>
        <div class="filter-button" @click="filter_oceania">In Oceania</div>
      </div>

      <div id="sort-wrap">
        <p><b>Sort by Name: </b></p>
        <select name="sort" id="sort" @change="onChange">
          <option value="1">Ascending (A-Z)</option>
          <option value="2">Descending(Z-A)</option>
        </select>
      </div>
    </div>

    <div class="country-content" v-for="country in paginated_countries" v-bind:key="country.name"> 
      <div class="country-header">{{ country.name }} </div><br>
      <b>Region: </b>{{ country.region }} <br>
      <b>Area Size: </b>{{ country.area }}
    </div>

    <div id="pagination-wrap">
      <button class="pagination-btn" @click="prev_page"> Previous </button>
      <button class="pagination-btn" @click="next_page">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ReizAssessment',
  props: {
    header: String
  },

  data(){
    return{
      original_list:[],
      countries_data:[],
      paginated_countries:[],
      index: 0,
      lithuania_area: 0,
      size: 10,
      number_of_pages: 0,
      page_number: 0,
      current_order: 0 // 1 if ascending, 2 if descending
    }
  },

  methods: {
    async fetch_data(){
      // Fetch results
      const results = await fetch("https://restcountries.com/v2/all?fields=name,region,area");
      const final_results = await results.json();
      this.original_list = final_results;
      this.countries_data = final_results;

      // Store Lithuania's area as global variable
      this.index = final_results.findIndex(final_results => final_results.name === 'Lithuania');
      this.lithuania_area = final_results[this.index].area;

      // Calculate pagination data
      var length = final_results.length;
      this.number_of_pages = (length/10);
      this.paginated_data();
      console.log ("Number of items: "+ this.size);
      console.log ("Number of pages: "+ this.number_of_pages);
    },

    next_page(){
      if (this.page_number < this.number_of_pages){
        this.page_number++;
        this.paginated_data();
      } else if(this.page_number > (this.number_of_pages-1)){
        window.alert("You have already reached the last page of the application.");
      }

      console.log("[Next page, Page number: ]" + this.page_number);
    },

    prev_page(){
      if (this.page_number >= 1){
        this.page_number--;
        this.paginated_data();
      } else if(this.page_number == 0){
        window.alert("You have already reached the very first page of the application.");
      }

      console.log("[Prev page, Page number: ]" + this.page_number);
    },

    paginated_data(){
      this.paginated_countries = []
      const start = this.page_number * this.size;
      const end = start + this.size;

      console.log(start, end);
      this.paginated_countries = this.countries_data.slice(start, end);

      if (this.current_order == 1)
        this.paginated_countries.sort(this.sort_ascending)
      else if (this.current_order == 2)
        this.paginated_countries.sort(this.sort_descending)
    },

    sort_ascending(country1, country2){
      if ( country1.name < country2.name ){
        return -1;
      } else if ( country1.name > country2.name){
        return 1;
      } else
        return 0;
    },

    sort_descending(country1, country2){
      if ( country2.name < country1.name ){
        return -1;
      } else if ( country2.name> country1.name){
        return 1;
      } else
        return 0;
    },
    
    filter_all(){
      // Ensure that original list is preserved
      console.log("[Filter-all BEFORE] Countries_data length: "+ this.countries_data.length);
      this.paginated_countries = [];
      this.countries_data = [];
      this.page_number=0;

      for(var i=0; i<this.original_list.length; i++){
          this.countries_data.push(this.original_list[i]);
      }

      // Calculate pagination data
      var length = this.countries_data.length;
      this.number_of_pages = (length/10) -1;
      console.log ("[Filter-all AFTER]  Page number: "+ this.page_number);
      console.log ("[Filter-all AFTER] Number of items: "+ this.size);
      console.log ("[Filter-all AFTER] Number of pages: "+ this.number_of_pages);
      console.log("[Filter-all AFTER] Countries_data length: "+ this.countries_data.length);

      this.paginated_data();
    },

    filter_lithuania(){
      // Ensure that original list is preserved
      this.paginated_countries = [];
      this.countries_data = [];
      this.page_number=0;
      console.log("[Filter-lithuania BEFORE] Countries_data length: "+ this.countries_data.length);
      console.log("[Filter-lithuania BEFORE] Original_list length: "+ this.original_list.length);
      console.log("[Filter-lithuania BEFORE] Lithuania area: " + this.lithuania_area);

      // Remove countries larger than Lithuania
      for(var i=0; i<this.original_list.length; i++){
        if (parseFloat(this.original_list[i].area) < parseFloat(this.lithuania_area))
          this.countries_data.push(this.original_list[i]);
      }

      // Calculate pagination data
      var length = this.countries_data.length;
      this.number_of_pages = (length/10) -1;
      console.log ("[Filter-lithuania AFTER]  Page number: "+ this.page_number);
      console.log ("[Filter-lithuania AFTER]  Number of items: "+ this.size);
      console.log ("[Filter-lithuania AFTER]  Number of pages: "+ this.number_of_pages);

      this.paginated_data();

      console.log("[Filter-lithuania AFTER] Countries_data length: "+ this.countries_data.length);
      console.log("[Filter-lithuania AFTER] Original_list length: "+ this.original_list.length);
    },

    filter_oceania(){
      // Ensure that original list is preserved
      this.paginated_countries = [];
      this.countries_data = [];
      this.page_number=0;
      console.log("[Filter-oceania BEFORE] Countries_data length: "+ this.countries_data.length);
      console.log("[Filter-oceania BEFORE] Original_list length: "+ this.original_list.length);

      // Remove countries not in Oceania
      for(var i=0; i<this.original_list.length; i++){
        if (this.original_list[i].region === 'Oceania')
          this.countries_data.push(this.original_list[i]);
      }

      // Calculate pagination data
      var length = this.countries_data.length;
      this.number_of_pages = (length/10) -1;
      console.log ("[Filter-oceania AFTER]  Page number: "+ this.page_number);
      console.log ("[Filter-oceania AFTER] Number of items: "+ this.size);
      console.log ("[Filter-oceania AFTER] Number of pages: "+ this.number_of_pages);

      this.paginated_data();

      console.log("[Filter-oceania AFTER] Countries_data length: "+ this.countries_data.length);
      console.log("[Filter-oceania AFTER] Original_list length: "+ this.original_list.length);
    },

    onChange(event){
      // If selected option is ascending, sort ascending
      if(event.target.value == 1){
        this.paginated_countries.sort(this.sort_ascending)
        this.current_order = 1;
        console.log("[onChange value 1] Paginated_countries length: "+ this.paginated_countries.length);
      }
      // Else, sort descending
      else{
        this.paginated_countries.sort(this.sort_descending)
        this.current_order = 2;
        console.log("[onChange value 2] Paginated_countries length: "+ this.paginated_countries.length);
      }
    }
  },

  mounted(){
    this.fetch_data();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  width:100%;
  height:100%;
  margin-top:0;
  margin-left:0;
  padding:5%;
  display:flex;
  flex-direction: column;
}

#header{
  width:max-content;
  text-align:left;
}

#options-wrap{
  width:90%;
  display:flex;
  justify-content: space-between;
}

#filter-button-wrap{
  width:40%;
  padding-top: 1%;
  padding-bottom:1%;
  display:flex;
  flex-direction:row;
  justify-content: left;
}

.filter-button{
  width:max-content;
  border:1px solid #2c3e50;
  border-radius:20px;
  padding:1%;
  margin:1%;
}

.filter-button:hover{
  color:#fff;
  background-color: #2c3e50;
}

#sort-wrap{
  width:40%;
  display:flex;
  justify-content: right;
  padding-top: 1%;
  padding-bottom:1%;
}

.country-content{
  width:85%;
  padding:1%;
  margin:0.5%;
  background:#42b983;
  text-align:left;
}

.country-header{
  font-size:1.5em;
  line-height:2em;
  color:#fff;
  border-bottom:1px solid #fff;
}

#pagination-wrap{
  width:90%;
}

.pagination-btn{
  width:max-content;
  background:none;
  border:1px solid #2c3e50;
  border-radius:20px;
  padding:1%;
  margin:1%;
}

.pagination-btn:hover{
  color:#fff;
  background-color: #2c3e50;
}

select{
  margin:1%;
  border-radius:20px;
}

a {
  color: #42b983;
}
</style>
