<template>
	<section class="dark:bg-gray-100 dark:text-gray-800">
		<div class="container flex flex-col justify-center p-6 mx-auto sm:py-12 lg:py-24 lg:flex-row ">
			<div class="flex items-center justify-center p-6 mt-8 lg:mt-0 h-72 sm:h-80 lg:h-96 xl:h-112 2xl:h-128">
				<img src="../assets/imgg.jpg" alt="" class="object-contain h-72 sm:h-80 lg:h-96 xl:h-112 2xl:h-128">
			</div>
			<div class="flex flex-col justify-center p-6 text-center rounded-sm lg:max-w-md xl:max-w-lg lg:text-left">
				<h1 class="text-5xl font-bold leading-none sm:text-6xl">Smart  
					<span class="dark:text-violet-600">Store </span>Chatbot
				</h1>
				<p class="mt-6 mb-8 text-lg sm:mb-12">The Smartphone Store Chatbot is an intelligent assistant designed to enhance the shopping
					<br class="hidden md:inline lg:hidden">experience in a smartphone distribution company. It guides users through
                    <br class="hidden md:inline lg:hidden">the process of selecting and purchasing a smartphone by interacting through a chat interface.
				</p>
				<div class="flex flex-col space-y-4 sm:items-center sm:justify-center sm:flex-row sm:space-y-0 sm:space-x-4 lg:justify-start">
					<router-link to="/chat" rel="noopener noreferrer" class="px-8 py-3 text-lg font-semibold rounded bg-gray-900 text-gray-50">Get Started</router-link>					
				</div>
			</div>
		</div>
	</section>


      
</template>