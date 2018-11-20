new Vue({
  el: '#app',
  data () {
    return {
      info: [],
      loading: true,
      errored: false,
      currentSort:'name',
      currentSortDir:'asc',
      pageSize:8,
      currentPage:1,
     }
    },
created:function() {
     axios // fetching the json data (we could fetching with many other ways but i prefer this one as it is for           //APIs too)
      .get('js/data.json')
      .then(response => {
        this.info = response.data.data;         
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
},
filters: {
  subInteger: function (num) {//pretifying the price 
    if (!num) return '0'
        num = num.toString().substring(0,4);
      return num;
  },
},
  methods: {
      //sorting on click
    sort:function(s) {
    //if s == current sort, reverse
    if(s === this.currentSort) {
      this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
    }
    this.currentSort = s;
  },
    nextPage:function() {
    if((this.currentPage*this.pageSize) < this.info.length) this.currentPage++;
    },
    prevPage:function() {
      if(this.currentPage > 1) this.currentPage--;
    }
},  
computed:{
  sortedProds:function() { //initial sort to the product items
    return this.info.sort((a,b) => {
      let modifier = 1;
      if(this.currentSortDir === 'desc') modifier = -1;
      if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
      if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
      return 0;
    }).filter((row, index) => {
		let start = (this.currentPage-1)*this.pageSize;
		let end = this.currentPage*this.pageSize;
		if(index >= start && index < end) return true;
	});
  }
}
      
})

