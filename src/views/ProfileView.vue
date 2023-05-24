<template>
  <div>
    <nav class="bg-blue-500 text-white p-4">
      <!-- ... existing code ... -->
    </nav>
    <div class="container mx-auto mt-4">
      <div class="flex flex-wrap">
        <div class="w-full md:w-1/4 p-4 bg-gray-200">
          <!-- Left Sidebar content -->
          Left Sidebar
        </div>
        <div class="w-full md:w-1/2 p-4 bg-white">
          <!-- Middle Content -->
          <WavesurferComponent />
        </div>
        <div class="w-full md:w-1/4 p-4 bg-gray-200">
          <!-- Right Sidebar content -->
          Right Sidebar
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import mockData from '../assets/mockdata';
import WavesurferComponent from '@/components/WavesurferComponent.vue';

export default {
  name: 'ProfileView',
  components: {
    WavesurferComponent,
  },
  data() {
    return {
      user: {
        name: 'John Doe',
        email: 'johndoe@example.com',
        profilePicture: 'path/to/profile-picture.jpg',
      },
      songs: [],
    };
  },
  mounted() {
    this.fetchSongs();
  },
  methods: {
    fetchSongs() {
      this.songs = mockData.songs;
    },
  },
};
</script>

<style>
/* ... existing styles ... */
</style>
