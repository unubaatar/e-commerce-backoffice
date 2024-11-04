<template>
  <div>
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>

<style >
@import url('https://fonts.googleapis.com/css2?family=Commissioner:wght@100..900&display=swap');

* {
  margin: 0;
  padding: 0;
  font-family: 'Commissioner', sans-serif; 
}

body {
  background-color:#E8E8E8;
}

::-webkit-scrollbar {
    width: 12px; 
    height: 12px; 
}

::-webkit-scrollbar-track {
    background: #f1f1f1; 
    border-radius: 10px; 
}

::-webkit-scrollbar-thumb {
    background: #888; 
    border-radius: 10px; 
}

::-webkit-scrollbar-thumb:hover {
    background: #555;
}

::-webkit-scrollbar-corner {
    background: #f1f1f1; 
} 
</style>