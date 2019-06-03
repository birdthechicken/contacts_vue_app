<template>
  <div class="home">
    <h1>New Contact</h1>
    <div>
      First Name: <input v-model="newContactFirstName">
    </div>

    <div>
      Middle Name: <input v-model="newContactMiddleName">
    </div>

    <div>
      Last Name: <input v-model="newContactLastName">
    </div>

    <div>
      Bio: <input v-model="newContactBio">
    </div>

    <div>
      Email: <input v-model="newContactEmail">
    </div>

    <button v-on:click="createContact()">Create</button>

    <h1>All Contacts</h1>

    <div v-for="contact in contacts">
      <h2>First Name: {{ contact.first_name }}</h2>
      <button v-on:click="showContact(contact)">Info</button>
      <div v-if="currentContact ===contact">
        <h3>Middle Name: {{ contact.middle_name }}</h3>
        <h3>Last Name: {{ contact.last_name }}</h3>
        <h3>Bio: {{ contact.bio }}</h3>
        <h3>Email: {{ contact.email }}</h3>

        <h3>Edit Contact</h3>
        <div>
          <div>
            First Name: <input v-model="contact.first_name">
          </div>

          <div>
            Middle Name: <input v-model="contact.middle_name">
          </div>

          <div>
            Last Name: <input v-model="contact.last_name">
          </div>

          <div>
            Bio: <input v-model="contact.bio">
          </div>

          <div>
            Email: <input v-model="contact.email">
          </div>
          <button v-on:click="updateContact(contact)">Update</button>
          <button v-on:click="destroyContact(contact)">Destroy</button>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      contacts: [],
      currentContact: {},
      newContactFirstName: "",
      newContactMiddleName: "",
      newContactLastName: "",
      newContactBio: "",
      newContactEmail: ""
    };
  },
  created: function() {
    axios.get("/api/contacts").then(response => {
      this.contacts = response.data;
    });
  },
  methods: {
    createContact: function () {

      var params = {
                    first_name: this.newContactFirstName,
                    middle_name: this.newContactMiddleName,
                    last_name: this.newContactLastName,
                    bio: this.newContactBio,
                    email: this.newContactEmail
                  };

      axios.post("/api/contacts/", params).then(response => {
        this.contacts.push(response.data);

        this.newContactFirstName = "";
        this.newContactMiddleName = "";
        this.newContactLastName = "";
        this.newContactBio = "";
        this.newContactEmail = "";
      });
    },
    showContact: function (inputContact) {
      if (this.currentContact === inputContact) {
        this.currentContact = {};
      } else {
        this.currentContact = inputContact;
      }
    },
    updateContact: function (inputContact) {
      var params = {
                    first_name: inputContact.first_name,
                    middle_name: inputContact.middle_name,
                    last_name: inputContact.last_name,
                    bio: inputContact.bio,
                    email: inputContact.email
                    };

      axios.patch("/api/contacts/" +inputContact.id, params).then(response => {
        console.log("Success", response.data);
      });
    },
    destroyContact: function (inputContact) {
      axios.delete("/api/contacts/" + inputContact.id).then(response => {
        var index = this.contacts.indexOf(inputContact);
        this.contacts.splice(index, 1);
      });
    }
  }
};
</script>
