<template>
  <div ref="root">
    <input 
       v-bind="inputProps"
       v-model="value"
       @keyup="search"
    />
    <ul
      ref="resultList"
      class="tag-list"
      v-if="this.dropdownValues != null"
    >
          <li
            v-for="tag in this.dropdownValues"
            :key="`tag-${tag}-list-item`"
            class="tag-list-item"
          >
            {{ tag }}
          </li>
    </ul>
  </div>    
</template>

<script>
  export default {
    data() {
      return {
        value: ""
      };      
    },
    props: {
      allEpisodes: {
        type: Array,
        required: true
      }
    },
    computed: {
      dropdownValues() {
        let values = [];
        this.allEpisodes.map(episode => {
          const title = episode.node.episode_title;
          const tags = episode.node.tags.map(tag => {
            values.push(tag);
          });
          values.push(title);
        });

        return this.distinct(values).filter(value => value.toLowerCase().includes(this.value.toLowerCase()));
      },
      inputProps() {
        return {
          class: "",
          value: this.value,
          role: 'combobox',
          autocomplete: 'off',
          autocapitalize: 'off',
          autocorrect: 'off',
          spellcheck: 'false',
          'aria-autocomplete': 'list',
          'aria-haspopup': 'listbox',
        }
      }
    },
    methods: {
      search() {        
        if (this.value.length < 1) { this.$emit('update:result', this.allEpisodes); }
        let filteredEpisodes = this.allEpisodes.filter(episode => {
          return episode.node.tags.includes(this.value.toLowerCase()) || episode.node.episode_title.toLowerCase().includes(this.value.toLowerCase());
        });
        this.$emit('update:result', filteredEpisodes);
      },
      distinct(list) {
        var u = {}, a = [];
        for(var i = 0, l = list.length; i < l; ++i){
            if(!u.hasOwnProperty(list[i])) {
                a.push(list[i]);
                u[list[i]] = 1;
            }
        }
        return a;
      }
    }
  }
</script>

<style lang="scss">
  .tag-list {
    padding: 0;

    &-item {
      list-style: none;
      margin: 0;
      padding: 0;
    }
  }
</style>
