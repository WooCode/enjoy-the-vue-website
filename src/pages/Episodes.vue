<script>
import EpisodePreview from '../components/EpisodePreview';
import TagsAutocomplete from '../components/TagsAutocomplete';

export default {
  data() {
    return {
      result: []
    }
  },
  metaInfo: {
    title: 'Episodes'
  },
  components: {
    EpisodePreview,
    TagsAutocomplete
  },
  methods: {
    search(input) {      
      if (input.length < 1) { return [] }
      return this.sortedEpisodes.filter(episode => {
        return node.tags.includes(input.toLowerCase()) || node.episode_title.toLowerCase().includes(input.toLowerCase());
      });
    }
  },
  computed: {
    sortedEpisodes() {
      return this.$page.allEpisode.edges
        .map(data => {
          return data.node;
        })
        .sort((currentItem, nextItem) => {
          // Sort in descending order
          if (nextItem.episode_number < currentItem.episode_number) {
            return -1;
          } else if (nextItem.episode_numberr > currentItem.episode_number) {
            return 1;
          } else {
            return 0;
          }
        });    
    }
  }
};
</script>

<template>
  <Layout>
    <main class="container episodes">
      <section class="container__section container__header">
        <div class="container__section-inner">
          <h1>Episodes</h1>
          <tags-autocomplete :allEpisodes="this.allEpisodes" :result.sync="result"/>
        </div>        
      </section>

      <div class="container__section">
        <div class="container__section-inner">
          <h2>Most Recent</h2>
          <ul class="episode-list" v-if="this.result.length === 0">
            <li
              v-for="node in this.result"
              :key="`episode-${node.episode_number}-list-item`"
              class="episode-list-item"
            >
              <episode-preview :episode="episode" />
            </li>
          </ul>
        </div>
      </div>
    </main>
  </Layout>
</template>

<style lang="scss">
@import '../styles/layout.scss';

.episode-list {
  padding: 0;

  &-item {
    list-style: none;
    margin: 0;
    padding: 0;
  }
}

.episodes {
  color: white;
}
</style>

<page-query>
query {
  allEpisode {
    edges {
      node {
        date_published
        episode_number
        episode_title
        excerpt
        tags
      }
    }
  }
}
</page-query>
