<template>
  <!-- First small trial project! In addition on Vue.js It can be further developed endlessly :) -->


  <div class="profile-container">
    <div class="header">
      <h1>TROOD. Profile</h1>
    </div>
    <div class="projects-section">
      <h2>Projects:</h2>
      <div class="create-project-box">
        <span>Create project</span>
        <span class="add-project-btn"><img src="./add.svg" alt="" /></span>
      </div>
    </div>
    <div class="projects-section second-projects-section">
      <h2>Tasks:</h2>
      <div class="create-project-box">
        <span>Create project</span>
        <span class="add-project-btn"><img src="./add.svg" alt="" /></span>
      </div>
    </div>

    <div class="profile-card">
      <div class="avatar-wrapper">
        <label class="avatar-label" for="avatar-upload">
          <div v-if="avatar" class="avatar-preview">
            <img :src="avatar" alt="Avatar" />
          </div>
          <div v-else class="avatar-placeholder">
            <span class="avatar-icon">
              <img src="./photo.svg" alt="camera-icon" class="photo.svg" />
            </span>
          </div>
        </label>
        <input
          id="avatar-upload"
          type="file"
          accept=".jpg, .jpeg, .png, .svg"
          @change="handleAvatarUpload"
          hidden
        />
      </div>

      <form class="profile-form" @submit.prevent="submitForm">
        <input type="text" v-model="profile.name" placeholder="Name" />
        <span v-if="errors.name" class="error-message">{{ errors.name }}</span>

        <input type="text" v-model="profile.surname" placeholder="Lastname" />
        <span v-if="errors.surname" class="error-message">{{ errors.surname }}</span>

        <input type="text" v-model="profile.jobTitle" placeholder="Job Title" />
        <span v-if="errors.jobTitle" class="error-message">{{ errors.jobTitle }}</span>

        <input type="tel" v-model="profile.phone" placeholder="Phone" />
        <span v-if="errors.phone" class="error-message">{{ errors.phone }}</span>

        <input type="email" v-model="profile.email" placeholder="Email" />
        <span v-if="errors.email" class="error-message">{{ errors.email }}</span>

        <input type="text" v-model="profile.address" placeholder="Address" />
        <span v-if="errors.address" class="error-message">{{ errors.address }}</span>

        <input type="text" v-model="profile.pitch" placeholder="Pitch" />
        <span v-if="errors.pitch" class="error-message">{{ errors.pitch }}</span>

        <label class="options">Show your profile in launchpad:</label>
        <div class="visibility-wrapper">
          <label>
            <input
              type="radio"
              name="visibility"
              value="private"
              v-model="profile.visibility"
            />
            Private
          </label>
          <label>
            <input
              type="radio"
              name="visibility"
              value="public"
              v-model="profile.visibility"
            />
            Public
          </label>
        </div>

        <div class="interests-wrapper">
          <label>The scopes of your interest:</label>
          <div class="interest-list">
            <span
              v-for="(interest, index) in profile.interests"
              :key="index"
              class="interest-tag"
            >
              {{ interest }} <button @click="removeInterest(index)">x</button>
            </span>
            <button @click.prevent="addInterest" class="add-btn">+</button>
          </div>
        </div>

        <div class="interests-wrapper">
          <label>Potential interests:</label>
          <div class="interest-list">
            <span
              v-for="(potential, index) in profile.potentialInterests"
              :key="index"
              class="interest-tag"
            >
              {{ potential }}
              <button @click="removePotentialInterest(index)">x</button>
            </span>
            <button @click.prevent="addPotentialInterest" class="add-btn">
              +
            </button>
          </div>
        </div>

        <div class="links-wrapper">
          <label>Your Links:</label>
          <button @click.prevent="addLink" class="add-btn">+</button>
          <div class="link-list">
            <div
              v-for="(link, index) in profile.links"
              :key="index"
              class="link-item"
            >
              <div class="link-fields">
                <input
                  v-model="link.siteName"
                  placeholder="Site Name"
                  class="site-name-input"
                />
                <input
                  v-model="link.url"
                  placeholder="Link"
                  class="link-input"
                />
              </div>

              <button @click="removeLink(index)" class="remove-btn">🗑️</button>
            </div>
          </div>
        </div>

        <div class="button-group">
          <button type="submit" class="save-btn">Save Profile</button>
  <button @click.prevent="clearAllFields" class="clear-btn">Clear</button>
 
</div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      avatar: null,
      profile: {
        name: "",
        surname: "",
        jobTitle: "",
        phone: "",
        email: "",
        address: "",
        pitch: "",
        visibility: "private",
        interests: [],
        potentialInterests: [],
        links: [],
      },
      errors: {
        name: null,
        surname: null,
        jobTitle: null,
        phone: null,
        email: null,
        address: null,
        pitch: null,
      },
    };
  },
  methods: {
    loadProfile() {
      const storedProfile = localStorage.getItem("profile");
      if (storedProfile) {
        const parsedProfile = JSON.parse(storedProfile);
        this.profile = parsedProfile;
        this.avatar = parsedProfile.avatar || null;
      }
    },
    handleAvatarUpload(event) {
      const file = event.target.files[0];
      if (!file) return;

      const validTypes = ["image/jpeg", "image/png", "image/jpg"];
      if (!validTypes.includes(file.type)) {
        alert("Only JPG, JPEG, and PNG files are allowed.");
        return;
      }
      if (file.size > 5 * 1024 * 1024) {
        alert("File size must be less than 5 MB.");
        return;
      }

      const reader = new FileReader();
      reader.onload = (e) => {
        this.avatar = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    validateForm() {
      let isValid = true;
      this.errors = {
        name: null,
        surname: null,
        jobTitle: null,
        phone: null,
        email: null,
        address: null,
        pitch: null,
      };

      if (!this.profile.name) {
        this.errors.name = "Name is required.";
        isValid = false;
      }
      if (!this.profile.surname) {
        this.errors.surname = "Surname is required.";
        isValid = false;
      }
      if (!this.profile.jobTitle) {
        this.errors.jobTitle = "Job title is required.";
        isValid = false;
      }
      const phoneRegex = /^[0-9]{10}$/;
      if (!this.profile.phone || !phoneRegex.test(this.profile.phone)) {
        this.errors.phone = "Phone number must be 10 digits.";
        isValid = false;
      }
      const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
      if (!this.profile.email || !emailRegex.test(this.profile.email)) {
        this.errors.email = "Enter a valid email address.";
        isValid = false;
      }
      if (!this.profile.address) {
        this.errors.address = "Address is required.";
        isValid = false;
      }
      if (!this.profile.pitch) {
        this.errors.pitch = "Pitch is required.";
        isValid = false;
      }

      return isValid;
    },
    submitForm() {
      if (this.validateForm()) {
        alert("Profile saved successfully!");
        this.saveProfile();
      }
    },
    saveProfile() {
      const profileData = { ...this.profile, avatar: this.avatar };
      localStorage.setItem("profile", JSON.stringify(profileData));
    },
    addInterest() {
      if (this.profile.interests.length < 10) {
        const newInterest = prompt("Enter a new interest:");
        if (newInterest && newInterest.length <= 30) {
          this.profile.interests.push(newInterest);
        } else {
          alert("Interest must be up to 30 characters long.");
        }
      } else {
        alert("You can add up to 10 interests only.");
      }
    },
    removeInterest(index) {
      this.profile.interests.splice(index, 1);
    },
    addPotentialInterest() {
      if (this.profile.potentialInterests.length < 10) {
        const newInterest = prompt("Enter a new potential interest:");
        if (newInterest && newInterest.length <= 30) {
          this.profile.potentialInterests.push(newInterest);
        } else {
          alert("Potential interest must be up to 30 characters long.");
        }
      } else {
        alert("You can add up to 10 potential interests only.");
      }
    },
    removePotentialInterest(index) {
      this.profile.potentialInterests.splice(index, 1);
    },
    addLink() {
      this.profile.links.push({ siteName: "", url: "" });
    },
    removeLink(index) {
      this.profile.links.splice(index, 1);
    },
    clearAllFields() {
      this.avatar = null;
      this.profile = {
        name: "",
        surname: "",
        jobTitle: "",
        phone: "",
        email: "",
        address: "",
        pitch: "",
        visibility: "private",
        interests: [],
        potentialInterests: [],
        links: [],
      };
      this.errors = {
        name: null,
        surname: null,
        jobTitle: null,
        phone: null,
        email: null,
        address: null,
        pitch: null,
      };
      localStorage.removeItem("profile");
      alert("The form is cleared!");
    },
  },
  mounted() {
    this.loadProfile();
  },
};
</script>

<style scoped>
/* styles for validation errors and input fields */
.error-message {
  color: red;
  font-size: 12px;
}


/* General Container */
.profile-container {
  display: flex;
  justify-content: end;
  padding: 40px;
}

/* Header */
.header {
  position: absolute;
  top: 10px;
  left: 30px;
  z-index: 10;
}

.header h1 {
  margin: 0;
  font-size: 32px;
  color: #49535c;
  font-family: "Roboto", sans-serif;
  font-weight: bold;
}
/* Profile Card */
.profile-card {
  position: relative;
  width: 500px;
  border-radius: 24px;
  background: linear-gradient(180deg, #e9f2f3 0%, #a6c5e3 100%);
  padding: 20px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

/* Edit Button */
button.edit-card-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: transparent;
  border: none;
  cursor: pointer;
  padding: 0;
  border-radius: 0;
}
button.edit-card-btn img {
  width: 24px;
  height: 24px;
}
/* Avatar */
.avatar-wrapper {
  text-align: center;
  margin-bottom: 20px;
}

.avatar-label {
  display: block;
  width: 120px;
  height: 120px;
  margin: auto;
  border-radius: 50%;
  border: 2px dashed #ccc;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f9f9f9;
  cursor: pointer;
}

.avatar-placeholder {
  font-size: 36px;
  color: #ccc;
}

.avatar-preview img {
  display: block;
  width: 120px;
  height: 120px;
  object-fit: cover;
  border-radius: 50%;
}
/* Form Elements */
form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

input {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid rgba(0, 0, 0, 0.38);
}

button {
  background: linear-gradient(180deg, #e9f2f3 0%, #a6c5e3 100%);
  border: none;
  text-align: center;
  cursor: pointer;
  border-radius: 15px;
  font-size: 13px;
}
button:hover {
  background: linear-gradient(180deg, #a6c5e3 0%, #e9f2f3 100%);
  transform: scale(1.05);
}

button.add-btn {
  margin-left: 4px;
  font-size: 22px;
  line-height: 22px;
}
/* Link List */
.link-list {
  margin-top: 10px;
}

.link-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  gap: 10px;
}

.link-fields {
  display: flex;
  flex-grow: 1;
  gap: 5px;
}

.site-name-input,
.link-input {
  border: none;
  border-bottom: 1px solid #ccc;
  background: transparent;
  padding: 5px;
  font-size: 14px;
}

.remove-btn {
  font-size: 20px;
 }

.profile-container {
  display: flex;
  justify-content: end;
  padding: 40px;
}

.profile-card {
  width: 500px;
  border-radius: 24px;
  background: linear-gradient(180deg, #e9f2f3 0%, #a6c5e3 100%);
  padding: 20px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.avatar-wrapper {
  text-align: center;
  margin-bottom: 20px;
}

.avatar-label {
  display: block;
  width: 120px;
  height: 120px;
  margin: auto;
  border-radius: 50%;
  border: 2px dashed #ccc;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f9f9f9;
  cursor: pointer;
}

label {
  color: #49535c;
  font-family: Roboto;
  font-size: 18px;
  font-style: normal;
  font-weight: 400;
  line-height: 32.494px;
}

.avatar-placeholder {
  font-size: 36px;
  color: #ccc;
}

.avatar-preview img {
  display: block;
  width: 120px;
  height: 120px;
  object-fit: cover;
  border-radius: 50%;
}

form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

input {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid rgba(0, 0, 0, 0.38);
}


button:hover {
  background: linear-gradient(180deg, #a6c5e3 0%, #e9f2f3 100%);
  transform: scale(1.05);
}
.interest-list {
 

  padding:2px 10px;
}

button.add-btn {
  border-radius: 10px;
  margin-right: 10px;
}

.interest-tag,
.potential-tag,
.link-tag {
  color: #49535c;
  font-family: Roboto;
  border-radius: 14px;
  padding: 2px 12px;
}
.interests-wrapper {
  display: flex;
}
.interest-tag {
  margin-right: 10px;
  border: 1px solid #49535c;
  font-size: 18px;
  font-style: normal;
}
.potential-tag {
  display: flex;
  align-items: center;
  gap: 5px;
  white-space: nowrap;
  color: #49535c;
  font-family: Roboto;
}

.site-name-input,
.link-input {
  padding: 5px;
  border: 1px solid rgba(0, 0, 0, 0.38);
  border-radius: 5px;
  width: calc(50% - 10px);
}

.remove-btn {
  border: none;
  background-color: #ff4d4d;
  color: rgb(30, 9, 9);
  border-radius: 5px;
  cursor: pointer;
  padding: 5px;
}

/* Projects */
.projects-section {
  position: absolute;
  top: 100px;
  left: 20px;
}

.projects-section h2 {
  font-size: 48px;
  font-weight: bold;
  color: #49535c;
  padding-left: 30px;
}

.create-project-box {
  display: flex;
  padding-left: 20px;
  justify-content: space-between;
  background: #d9d9d9;
  border-radius: 15px;
  width: 300px;
  height: 200px;
  flex-shrink: 0;
}

.create-project-box span {
  color: rgba(0, 0, 0, 0.5);
  font-family: Inter;
  font-size: 20px;
  font-style: normal;
  font-weight: 500;
  margin: 20px;
}

.add-project-btn img {
  padding-top: 110px;
  cursor: pointer;
  width: 50px;
  height: 50px;
}
.second-projects-section {
  position: absolute;
  top: calc(20px + 8cm + 90px);
  left: 20px;
}
button-group {
  display: flex;
  justify-content: space-between; 
  align-items: center; 
  margin-top: 20px;
  border: 2px solid #49535c; 
  border-radius: 15px;
  padding: 10px;
  background-color: #f5f5f5; 
}

.clear-btn, .save-btn {
  background: linear-gradient(180deg, #76c2e7 0%, #4990c0 100%);
  color: white;
  border: none;
  border-radius: 10px;
  padding: 10px 20px;
  cursor: pointer;
  font-size: 14px;
  font-weight: bold;
  transition: transform 0.2s, background-color 0.2s;
}

.clear-btn:hover, .save-btn:hover {
  
  background: #d2cdcd; 
  transform: scale(1.05); 
}
.clear-btn {
  margin-left: 30px; 
}
.save-btn {
  background: linear-gradient(180deg, #76c2e7 0%, #4990c0 100%);
}

.save-btn:hover {
  background:  #d2cdcd; 
}
</style>
