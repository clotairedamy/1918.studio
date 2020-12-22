<template>
  <div
    class="py-4 px-6 text-center bg-yellow-500 lg:flex-shrink-0 lg:flex lg:flex-col lg:justify-center lg:p-12 shadow-lg rounded"
  >
    <!--<p class="text-lg leading-6 font-medium text-gray-900">UI</p>-->
    <div
      class="mt-4 flex items-center justify-center text-5xl font-extrabold text-gray-800"
    >
      <span> {{title}} </span>
    </div>
    <div class="mt-4 text-md">
      <a href="#" class="font-medium text-gray-800">
        {{content}}
      </a>
    </div>
    <!--<div class="mt-6">
              <div class="rounded-md shadow">
                <a
                  href="#"
                  class="flex items-center justify-center px-5 py-3 border border-transparent text-base font-medium rounded-md text-gray-100 bg-gray-900 hover:bg-gray-600"
                >
                  Get in Touch
                </a>
              </div>
            </div>-->
  </div>
</template>

<script>
export default {
    props: {
        title: {type: String, default: "Please change title"},
        content: {type:String, default: "Please write something here"}
    }
};
</script>