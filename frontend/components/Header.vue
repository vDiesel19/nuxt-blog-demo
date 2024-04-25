<template>
  <header>
    <p>Recipe Browser 3000</p>
  </header>
</template>

<style>
header {
  background-color: #0d334c;
  padding: 20px;
  text-align: left;
  border-bottom: 1px solid #21445c;
}
header p {
  font-weight: 400;
  letter-spacing: 1px;;
  text-transform: uppercase;
  max-width: 1440px;
  color: #fff;
  margin: 0 auto;
  padding-left: 40px;
}
@media (max-width: 1024px) {
  header {
    text-align: center;
  }
  header p {
    margin: 0;
    padding: 0;
  }
}
</style>