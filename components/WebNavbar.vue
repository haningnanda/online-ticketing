<template>
  <b-navbar class="Navbar" toggleable="lg" type="dark">
    <b-navbar-brand href="#">Teecket</b-navbar-brand>
    <b-form-group class="SearchBarContainer">
      <b-input-group>
        <b-form-input
          v-model="searchInput"
          placeholder="Cari sesuatu..."
          type="text"
          class="SearchBar"
          @input="updateSearchQuery"
        />
      </b-input-group>
    </b-form-group>
    <b-navbar-nav v-if="!isUserLoggedIn" class="RightNavigation">
      <b-nav-item href="#">Cari Event</b-nav-item>
      <b-nav-item href="/registerOrganizerPage">Menjadi Organizer</b-nav-item>
      <b-button class="NavButton" href="/loginPage">Masuk</b-button>
    </b-navbar-nav>
    <b-navbar-nav v-if="isUserLoggedIn" class="RightNavigation">
      <template v-if="isUserType('user')">
        <b-nav-item href="#">Cari Event</b-nav-item>
        <b-nav-item href="#">My Tickets</b-nav-item>
        <div class="UserAvatarButton" @click="openDropdown">
          <img src="https://iili.io/Jk1PRV4.jpg" class="UserAvatar" />
          <b-dropdown ref="profileDropdown1" variant="link" no-caret right>
            <b-dropdown-item @click="navigateToProfile">
              <div class="ProfileDropdownItem">
                <div class="AvatarContainer">
                  <img src="https://iili.io/Jk1PRV4.jpg" class="AvatarImage" />
                </div>
                <div class="UserInfo">
                  <div class="DropdownProfileName">Lebaran</div>
                  <div class="DropdownProfileEmail">aasasdbdowqdidqwdhqwidqwdqwdiqw.gmail.com</div>
                </div>
              </div>
            </b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="navigateToProfile">Akun</b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="logout">Logout</b-dropdown-item>
          </b-dropdown>
        </div>
        
      </template>
      <template v-else-if="isUserType('organizer')">
        <b-nav-item href="#">Cari Event</b-nav-item>
        <b-nav-item href="#">My Events</b-nav-item>
        <b-nav-item href="#">Dashboard</b-nav-item>
        <div class="UserAvatarButton" @click="openDropdown">
          <img src="https://iili.io/Jk1PRV4.jpg" class="UserAvatar" />
          <b-dropdown ref="profileDropdown2" variant="link" no-caret right>
            <b-dropdown-item @click="navigateToProfile">
              <div class="ProfileDropdownItem">
                <div class="AvatarContainer">
                  <img src="https://iili.io/Jk1PRV4.jpg" class="AvatarImage" />
                </div>
                <div class="UserInfo">
                  <div class="DropdownProfileName">Lebaran</div>
                  <div class="DropdownProfileEmail">aasasdbdowqdidqwdhqwidqwdqwdiqw.gmail.com</div>
                </div>
              </div>
            </b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="navigateToProfile">Akun</b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="navigateToSubscription">Langganan</b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="logout">Logout</b-dropdown-item>
          </b-dropdown>
        </div>
      </template>    
      <template v-else-if="isUserType('admin')">
        <b-nav-item href="#">Cari Event</b-nav-item>
        <b-nav-item href="#">User Management</b-nav-item>
        <b-nav-item href="#">Dashboard</b-nav-item>
        <div class="UserAvatarButton" @click="openDropdown">
          <img src="https://iili.io/Jk1PRV4.jpg" class="UserAvatar" />
          <b-dropdown ref="profileDropdown3" variant="link" no-caret right>
            <b-dropdown-item @click="navigateToProfile">
              <div class="ProfileDropdownItem">
                <div class="AvatarContainer">
                  <img src="https://iili.io/Jk1PRV4.jpg" class="AvatarImage" />
                </div>
                <div class="UserInfo">
                  <div class="DropdownProfileName">Lebaran</div>
                  <div class="DropdownProfileEmail">aasasdbdowqdidqwdhqwidqwdqwdiqw.gmail.com</div>
                </div>
              </div>
            </b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="navigateToProfile">Akun</b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item @click="logout">Logout</b-dropdown-item>
          </b-dropdown>
        </div>
      </template>
    </b-navbar-nav>
  </b-navbar>
</template>

<script>
import axios from 'axios';

export default {
  name: 'WebNavbar',

  data() {
    return {
      searchInput: this.searchQuery,
      dropdownOpen: false,
      loggedIn:true,
      userType:'user',
      userData:{},
    };
  },
  computed: {
    isUserLoggedIn() {
      return this.loggedIn;
    },
  },
  watch: {
    // Watch changes to props and update local data accordingly
    searchQuery(newVal) {
      this.searchInput = newVal;
    },
  },
  methods: {
    updateSearchQuery() {
      this.$emit('update:searchQuery', this.searchInput);
    },
    toggleDropdown() {
      this.dropdownOpen = !this.dropdownOpen;
      console.log("dropdown",this.dropdownOpen)
    },
    openDropdown() {
      if(this.isUserType('user')){
        this.$refs.profileDropdown1?.show();
      }
      if(this.isUserType('organizer')){
        this.$refs.profileDropdown2?.show();
      }
      if(this.isUserType('admin')){
        this.$refs.profileDropdown3?.show();
      }
    },
    navigateToProfile() {
      // To be implemented
    },
    navigateToSubscription() {
      // To be implemented
    },
    logout() {
      // logout
    },
    isUserType(type) {
      return this.userType === type;
    },
    async checkLoginStatus() {
      try {
        // Make API request to check user login status
        const response = await axios.get('/api/check-login');
        if (response.data.loggedIn) {
          this.isUserLoggedIn = true;
          this.fetchUserData(); // Fetch user data if logged in
        } else {
          this.isUserLoggedIn = false;
          this.userData = null; // Reset user data
        }
      } catch (error) {
        // eslint-disable-next-line no-console
        console.error('Error checking login status:', error);
      }
    },
    async fetchUserData() {
      try {
        // Make API request to fetch user data
        const response = await axios.get('/api/user-data');
        this.userData = response.data; // Set user data
      } catch (error) {
        // eslint-disable-next-line no-console
        console.error('Error fetching user data:', error);
      }
    },
  },
};
</script>
<style scoped>
/* Navbar */
.Navbar {
  display: flex;
  height: 8vh;
  align-items: center;
  justify-content: space-around;
  background-color: #0e9d6e;
  gap: 100px;
  color: white;
}

/* Search bar */
.SearchBarContainer {
  display: flex;
  align-items: center;
  margin-top: 1rem;
  border-radius: 5px;
  background-color: white;
}

.SearchBar {
  display: block;
  width: 60vh;
  padding: 9px 4px 9px 40px;
  border-radius: 5px;
  background: transparent url("/search.svg") no-repeat 13px;
}

/* Right navigation */
.RightNavigation {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  font-size: 1rem;
}

.NavButton {
  border-radius: 18px;
  background-color: white;
  color: black;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 10vh;
  height: 40px;
  font-size: 1rem;
}
.b-nav-items{
    color: white;
  }

.UserAvatarButton {
  display: flex;
  align-items: center;
  cursor: pointer;
  position: relative;
}

.UserAvatarButton img {
  width: 40px;
  height: 40px; 
  border-radius: 50%;
}
.dropdown-menu {
    max-width: 16rem !important;
}
.b-dropdown-toggle {
  display: none !important; 
  visibility: hidden;
}

.ProfileDropdownItem {
  display: flex;
  align-items: center;
  gap: 20px;
}

.AvatarContainer {
  width: 25%;
  max-width: 50px; 
}

.AvatarImage {
  width: 100%;
  border-radius: 50%;
}

.UserInfo {
  flex-grow: 1;
}
.DropdownProfileName{
  font-size: 1rem;
  overflow: hidden;
  text-overflow: ellipsis;
}
.DropdownProfileEmail{
  font-size: 0.75rem;
  overflow: hidden;
  text-overflow: ellipsis;
}
@media screen and (max-width: 768px) {
  .Navbar {
    justify-content: space-between; 
  }
}
</style>