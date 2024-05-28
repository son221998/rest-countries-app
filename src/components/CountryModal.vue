<template>
    <div class="modal">
      <div class="modal-content">
        <span class="close" @click="$emit('close')">&times;</span>
        <h2>{{ country.name.official }}</h2>
        <p><strong>Native Name:</strong> {{ country.nativeName }}</p>
        <p><strong>Alternative Names:</strong> {{ country.altSpellings.join(', ') }}</p>
        <p><strong>Country Code (2):</strong> {{ country.cca2 }}</p>
        <p><strong>Country Code (3):</strong> {{ country.cca3 }}</p>
        <p><strong>Calling Codes:</strong> {{ country.idd.root }}{{ country.idd.suffixes.join(', ') }}</p>
        <img :src="country.flags.png" :alt="country.name.official + ' flag'">
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: ['country']
  };
  </script>
  
  <style>
  .modal {
    display: block;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgb(0,0,0);
    background-color: rgba(0,0,0,0.4);
  }
  .modal-content {
    background-color: #fefefe;
    margin: 15% auto;
    padding: 20px;
    border: 1px solid #888;
    width: 80%;
  }
  .close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
  }
  .close:hover,
  .close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
  }
  </style>
  