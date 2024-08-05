<template>
  <div class="kbu-autocomplete">
    <aside class="details-wrap">
      <strong> Autocomplete Search Demo</strong>
      <p>
        Implementing Search for the data of user records which includes (Name, email, company name,
        phone number, address). The order of search results will be in the below order
      </p>
      <ul>
        <li>Name - Starting with search key</li>
        <li>Name - Contains the search key</li>
        <li>Any other field data (email,company,address,phone) - Starting with the search key</li>
        <li>Any other field data (email,company,address,phone) - Containing the search key</li>
      </ul>
    </aside>
    <div class="kbu-search-wrap">
      <div class="kbu-search">
        <input
          class="kbu-input"
          type="text"
          placeholder="Type here to search.."
          id="searchuser"
          v-model="searchkey"
          @input="searchUsers"
        />
      </div>
      <div class="kbu-result" id="result">
        <div class="kbu-user-wrap" v-for="(user, index) in users" :key="index">
          <div class="userimg"><img src="http://placehold.it/32x32" /></div>
          <div class="user-desc">
            <span v-html="user.primary"></span>
            <p v-html="user.secondary"></p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import userData from '@/assets/data'
export default {
  name: 'AutocompleteComponent',
  data() {
    return {
      searchkey: '',
      users: [],
      debounceDelay: 300
    }
  },
  methods: {
    debounce(func, delay) {
      let debounceTimeout
      return function (...args) {
        clearTimeout(debounceTimeout)
        debounceTimeout = setTimeout(() => func.apply(this, args), delay)
      }
    },
    getUsers: function () {
      const searchkey = this.searchkey.trim().toLowerCase()
      if (searchkey === '') {
        this.users = []
        return
      }
      const { Nstarts, NContains, SContains } = this.formatUsers(searchkey)
      this.users = [...Nstarts, ...NContains, ...SContains]
    },
    searchUsers() {
      this.debounce(this.getUsers, this.debounceDelay)()
    },
    formatUsers(searchkey) {
      const Nstarts = [],
        NContains = [],
        SContains = []

      userData.forEach((user) => {
        const secondary = this.findSecondaryMatch(user, searchkey)
        const IsName = user.displayName.toLowerCase().indexOf(searchkey)

        const userObj = {
          primary: this.highlightMatched(user.displayName, searchkey),
          secondary: this.highlightMatched(secondary || user.company, searchkey)
        }

        if (IsName > -1) {
          IsName === 0 ? Nstarts.push(userObj) : NContains.push(userObj)
        } else if (secondary) {
          SContains.push(userObj)
        }
      })

      return { Nstarts, NContains, SContains }
    },
    findSecondaryMatch(user, searchkey) {
      const { email, company, address, phone } = user
      const searchFields = [email, company, address, phone].map((field) => field.toLowerCase())
      return searchFields.find((field) => field.includes(searchkey)) || ''
    },
    highlightMatched(content, searchkey) {
      if (!content) return ''
      const regex = new RegExp(`(${searchkey})`, 'gi')
      return content.replace(regex, `<em class="highlight">$1</em>`)
    },
    removeResults() {
      this.users = []
    }
  }
}
</script>

<style>
.kbu-autocomplete * {
  box-sizing: border-box;
}

.kbu-autocomplete {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: calc(100% - 100px);
  margin: auto;
  height: 100vh;
}

.kbu-search-wrap {
  width: 400px;
  position: fixed;
  top: 200px;
  right: 100px;
}

.kbu-input {
  width: 100%;
  outline: none;
  font-size: 16px;
  border: 1px solid #ddd;
  padding: 0 15px;
  border-radius: 3px;
  height: 35px;
}

.kbu-input:focus {
  border: 1px solid rebeccapurple;
  box-shadow: inset 0 0 4px rebeccapurple;
}

.kbu-result {
  max-height: 245px;
  overflow: auto;
  border: 1px solid #ddd;
  border-radius: 3px;
  margin-top: 5px;
  box-shadow: 0 0 4px #7b757563;
}

.kbu-user-wrap {
  display: flex;
  align-items: center;
  border-bottom: 1px solid #ccc;
  padding: 10px;
  cursor: pointer;
}

.kbu-user-wrap:hover {
  background-color: #eee;
}

.userimg {
  height: 40px;
  width: 40px;
  border-radius: 100%;
  overflow: hidden;
  flex-shrink: 0;
  margin-right: 15px;
}

.userimg img {
  height: 100%;
  width: 100%;
}

.user-desc span {
  color: #333;
  font-size: 15px;
}

.user-desc p {
  color: #666;
  font-size: 13px;
  margin-top: 3px;
}

.user-desc p,
.user-desc span,
.user-desc {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.kbu-autocomplete .highlight {
  font-style: normal;
  color: #d86226;
}

aside {
  line-height: 27px;
  width: 50%;
}

@media (prefers-color-scheme: dark) {
  .details-wrap {
    color: white;
  }

  .kbu-result {
    border: 1px solid #ddd;
    box-shadow: 0 0 4px #7b757563;
  }

  .kbu-search-wrap {
    background-color: #202020;
  }

  .kbu-input {
    background: transparent;
    border: 1px solid #bbb;
    color: white;
  }

  .kbu-input:focus {
    border: 1px solid #bbb;
    box-shadow: inset 0 0 4px #bbb;
  }

  .kbu-user-wrap {
    border-bottom: 1px solid #ccc;
  }

  .kbu-user-wrap:hover {
    background-color: #373737;
  }

  .user-desc span {
    color: white;
  }

  .user-desc p {
    color: #666;
  }
}
</style>
