<template>
  <div class="container">
    <div>
      <h1 class="title">
        issue-manager-by-nuxt
      </h1>
      <h2 class="subtitle">
        Let&#39;s try it!😊
      </h2>
      <form class="w-full max-w-sm">
        <div class="md:flex md:items-center mb-6">
          <div class="md:w-1/3">
            <label
              class="block text-gray-500 font-bold md:text-right mb-1 md:mb-0 pr-4"
              for="inline-token"
            >
              required: GitHub Token
            </label>
          </div>
          <div class="md:w-2/3">
            <input
              id="inline-token"
              v-model="token"
              class="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"
              type="password"
              placeholder="******************"
            />
          </div>
        </div>
      </form>
      <div
        v-for="issue in issues"
        :key="issue.node.id"
        class="max-w-sm rounded overflow-hidden shadow-lg"
      >
        <div class="px-6 py-4">
          <div class="font-bold text-xl mb-2">
            <a :href="issue.node.url" target="_blank">{{ issue.node.title }}</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const configs = {
  githubTokenLength: 40,
  githubApiUrl: 'https://api.github.com/graphql',
  githubOwner: 'hidecharo',
  githubName: 'issue-manager-by-nuxt'
}

export default {
  data() {
    return {
      token: '',
      issues: []
    }
  },

  watch: {
    async token(newToken, oldToken) {
      console.log(newToken, oldToken)
      await this.fetchIssue()
    }
  },

  async mounted() {
    await this.fetchIssue()
  },

  methods: {
    async fetchIssue() {
      if (this.token.length !== configs.githubTokenLength) return

      const query = `query($owner: String!, $name: String!) {
        repository(owner: $owner, name: $name) {
          issues(first: 100, states: OPEN) {
            edges { node { id, url, title } }
          }
        }
      }`
      const variables = {
        owner: configs.githubOwner,
        name: configs.githubName
      }
      const payload = {
        query,
        variables
      }
      const headers = {
        Authorization: `Bearer ${this.token}`,
        'Content-Type': 'application/json; charset=UTF-8'
      }
      const options = {
        headers
      }
      const response = await this.$axios.$post(
        configs.githubApiUrl,
        payload,
        options
      )
      this.issues = response.data.repository.issues.edges
    }
  }
}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
  @apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
