<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FreelanceHub - Freelance Bidding Platform</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f9fafb;
        }
        .modal {
            transition: opacity 0.3s ease, transform 0.3s ease;
        }
        .modal-enter {
            opacity: 0;
            transform: translateY(-20px);
        }
        .modal-enter-active {
            opacity: 1;
            transform: translateY(0);
        }
        .tab-active {
            border-bottom: 3px solid #4f46e5;
            color: #4f46e5;
        }
        .progress-bar {
            transition: width 0.5s ease;
        }
        .message-bubble-client {
            background-color: #e0e7ff;
            border-radius: 18px 18px 18px 4px;
        }
        .message-bubble-freelancer {
            background-color: #f3f4f6;
            border-radius: 18px 18px 4px 18px;
        }
    </style>
</head>
<body>
    <div id="app" class="min-h-screen flex flex-col">
        <!-- Navigation -->
        <nav class="bg-white shadow-sm">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex justify-between h-16">
                    <div class="flex items-center">
                        <div class="flex-shrink-0 flex items-center">
                            <span class="text-indigo-600 text-xl font-bold">FreelanceHub</span>
                        </div>
                        <div class="hidden sm:ml-6 sm:flex sm:space-x-8">
                            <a href="#" class="border-indigo-500 text-gray-900 inline-flex items-center px-1 pt-1 border-b-2 text-sm font-medium" @click="setActivePage('home')">
                                Home
                            </a>
                            <a href="#" class="border-transparent text-gray-500 hover:border-gray-300 hover:text-gray-700 inline-flex items-center px-1 pt-1 border-b-2 text-sm font-medium" @click="setActivePage('projects')">
                                Browse Projects
                            </a>
                            <a href="#" class="border-transparent text-gray-500 hover:border-gray-300 hover:text-gray-700 inline-flex items-center px-1 pt-1 border-b-2 text-sm font-medium" @click="setActivePage('freelancers')">
                                Find Freelancers
                            </a>
                        </div>
                    </div>
                    <div class="hidden sm:ml-6 sm:flex sm:items-center">
                        <button class="bg-white p-1 rounded-full text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="toggleNotifications">
                            <span class="sr-only">View notifications</span>
                            <svg class="h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
                            </svg>
                            <span v-if="notifications.length > 0" class="absolute top-0 right-0 block h-2 w-2 rounded-full bg-red-400 ring-2 ring-white"></span>
                        </button>

                        <div class="ml-3 relative">
                            <div>
                                <button @click="toggleUserMenu" class="bg-white flex text-sm rounded-full focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" id="user-menu-button" aria-expanded="false" aria-haspopup="true">
                                    <span class="sr-only">Open user menu</span>
                                    <div class="h-8 w-8 rounded-full bg-indigo-100 flex items-center justify-center text-indigo-800 font-semibold">
                                        {{ currentUser.name.charAt(0) }}
                                    </div>
                                </button>
                            </div>
                            <div v-if="showUserMenu" class="origin-top-right absolute right-0 mt-2 w-48 rounded-md shadow-lg py-1 bg-white ring-1 ring-black ring-opacity-5 focus:outline-none" role="menu" aria-orientation="vertical" aria-labelledby="user-menu-button" tabindex="-1">
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100" role="menuitem" tabindex="-1" @click="setActivePage('dashboard')">Dashboard</a>
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100" role="menuitem" tabindex="-1" @click="setActivePage('profile')">Your Profile</a>
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100" role="menuitem" tabindex="-1" @click="toggleRoleSwitch">Switch to {{ currentUser.role === 'client' ? 'Freelancer' : 'Client' }}</a>
                                <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100" role="menuitem" tabindex="-1" @click="logout">Sign out</a>
                            </div>
                        </div>
                    </div>
                    <div class="-mr-2 flex items-center sm:hidden">
                        <button @click="toggleMobileMenu" class="bg-white inline-flex items-center justify-center p-2 rounded-md text-gray-400 hover:text-gray-500 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-indigo-500" aria-expanded="false">
                            <span class="sr-only">Open main menu</span>
                            <svg class="block h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                            </svg>
                        </button>
                    </div>
                </div>
            </div>

            <div v-if="showMobileMenu" class="sm:hidden">
                <div class="pt-2 pb-3 space-y-1">
                    <a href="#" class="bg-indigo-50 border-indigo-500 text-indigo-700 block pl-3 pr-4 py-2 border-l-4 text-base font-medium" @click="setActivePage('home')">Home</a>
                    <a href="#" class="border-transparent text-gray-600 hover:bg-gray-50 hover:border-gray-300 hover:text-gray-800 block pl-3 pr-4 py-2 border-l-4 text-base font-medium" @click="setActivePage('projects')">Browse Projects</a>
                    <a href="#" class="border-transparent text-gray-600 hover:bg-gray-50 hover:border-gray-300 hover:text-gray-800 block pl-3 pr-4 py-2 border-l-4 text-base font-medium" @click="setActivePage('freelancers')">Find Freelancers</a>
                </div>
                <div class="pt-4 pb-3 border-t border-gray-200">
                    <div class="flex items-center px-4">
                        <div class="flex-shrink-0">
                            <div class="h-10 w-10 rounded-full bg-indigo-100 flex items-center justify-center text-indigo-800 font-semibold">
                                {{ currentUser.name.charAt(0) }}
                            </div>
                        </div>
                        <div class="ml-3">
                            <div class="text-base font-medium text-gray-800">{{ currentUser.name }}</div>
                            <div class="text-sm font-medium text-gray-500">{{ currentUser.email }}</div>
                        </div>
                        <button class="ml-auto bg-white flex-shrink-0 p-1 rounded-full text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                            <span class="sr-only">View notifications</span>
                            <svg class="h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
                            </svg>
                        </button>
                    </div>
                    <div class="mt-3 space-y-1">
                        <a href="#" class="block px-4 py-2 text-base font-medium text-gray-500 hover:text-gray-800 hover:bg-gray-100" @click="setActivePage('dashboard')">Dashboard</a>
                        <a href="#" class="block px-4 py-2 text-base font-medium text-gray-500 hover:text-gray-800 hover:bg-gray-100" @click="setActivePage('profile')">Your Profile</a>
                        <a href="#" class="block px-4 py-2 text-base font-medium text-gray-500 hover:text-gray-800 hover:bg-gray-100" @click="toggleRoleSwitch">Switch to {{ currentUser.role === 'client' ? 'Freelancer' : 'Client' }}</a>
                        <a href="#" class="block px-4 py-2 text-base font-medium text-gray-500 hover:text-gray-800 hover:bg-gray-100" @click="logout">Sign out</a>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Main Content -->
        <main class="flex-grow">
            <!-- Home Page -->
            <div v-if="activePage === 'home'" class="bg-white">
                <!-- Hero Section -->
                <div class="relative bg-gradient-to-r from-indigo-600 to-purple-600">
                    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-24">
                        <div class="text-center">
                            <h1 class="text-4xl tracking-tight font-extrabold text-white sm:text-5xl md:text-6xl">
                                <span class="block">Find the perfect freelance</span>
                                <span class="block">services for your business</span>
                            </h1>
                            <p class="mt-3 max-w-md mx-auto text-base text-indigo-100 sm:text-lg md:mt-5 md:text-xl md:max-w-3xl">
                                Connect with talented freelancers, post projects, and get work done efficiently.
                            </p>
                            <div class="mt-10 max-w-md mx-auto sm:flex sm:justify-center md:mt-12">
                                <div class="rounded-md shadow">
                                    <a href="#" class="w-full flex items-center justify-center px-8 py-3 border border-transparent text-base font-medium rounded-md text-indigo-700 bg-white hover:bg-gray-50 md:py-4 md:text-lg md:px-10" @click="setActivePage('projects')">
                                        Browse Projects
                                    </a>
                                </div>
                                <div class="mt-3 rounded-md shadow sm:mt-0 sm:ml-3">
                                    <a href="#" class="w-full flex items-center justify-center px-8 py-3 border border-transparent text-base font-medium rounded-md text-white bg-indigo-800 hover:bg-indigo-900 md:py-4 md:text-lg md:px-10" @click="openPostProjectModal">
                                        Post a Project
                                    </a>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Popular Categories -->
                <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
                    <h2 class="text-3xl font-extrabold text-gray-900 text-center mb-12">
                        Popular Categories
                    </h2>
                    <div class="grid grid-cols-2 gap-6 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5">
                        <div v-for="category in categories" :key="category.id" class="bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow duration-300 p-6 text-center cursor-pointer" @click="filterProjectsByCategory(category.id)">
                            <div class="text-indigo-600 mb-3 flex justify-center">
                                <svg class="w-10 h-10" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3zM6 8a2 2 0 11-4 0 2 2 0 014 0zM16 18v-3a5.972 5.972 0 00-.75-2.906A3.005 3.005 0 0119 15v3h-3zM4.75 12.094A5.973 5.973 0 004 15v3H1v-3a3 3 0 013.75-2.906z"></path>
                                </svg>
                            </div>
                            <h3 class="text-lg font-medium text-gray-900">{{ category.name }}</h3>
                            <p class="mt-2 text-sm text-gray-500">{{ category.count }} projects</p>
                        </div>
                    </div>
                </div>

                <!-- How It Works -->
                <div class="bg-gray-50 py-16">
                    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                        <h2 class="text-3xl font-extrabold text-gray-900 text-center mb-12">
                            How FreelanceHub Works
                        </h2>
                        <div class="grid grid-cols-1 gap-8 md:grid-cols-3">
                            <div class="bg-white rounded-lg shadow-sm p-6 text-center">
                                <div class="bg-indigo-100 rounded-full w-16 h-16 flex items-center justify-center mx-auto mb-4">
                                    <span class="text-indigo-600 text-2xl font-bold">1</span>
                                </div>
                                <h3 class="text-xl font-medium text-gray-900 mb-2">Post a Project</h3>
                                <p class="text-gray-500">Describe your project and the skills required. Set your budget and timeline.</p>
                            </div>
                            <div class="bg-white rounded-lg shadow-sm p-6 text-center">
                                <div class="bg-indigo-100 rounded-full w-16 h-16 flex items-center justify-center mx-auto mb-4">
                                    <span class="text-indigo-600 text-2xl font-bold">2</span>
                                </div>
                                <h3 class="text-xl font-medium text-gray-900 mb-2">Receive Bids</h3>
                                <p class="text-gray-500">Freelancers will submit proposals with their rates, timeline, and approach.</p>
                            </div>
                            <div class="bg-white rounded-lg shadow-sm p-6 text-center">
                                <div class="bg-indigo-100 rounded-full w-16 h-16 flex items-center justify-center mx-auto mb-4">
                                    <span class="text-indigo-600 text-2xl font-bold">3</span>
                                </div>
                                <h3 class="text-xl font-medium text-gray-900 mb-2">Collaborate & Pay</h3>
                                <p class="text-gray-500">Choose the best freelancer, work together, and release payment when satisfied.</p>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Featured Freelancers -->
                <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
                    <h2 class="text-3xl font-extrabold text-gray-900 text-center mb-12">
                        Top Rated Freelancers
                    </h2>
                    <div class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
                        <div v-for="freelancer in featuredFreelancers" :key="freelancer.id" class="bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow duration-300 overflow-hidden">
                            <div class="p-6">
                                <div class="flex items-center">
                                    <div class="h-12 w-12 rounded-full bg-indigo-100 flex items-center justify-center text-indigo-800 font-semibold text-lg">
                                        {{ freelancer.name.charAt(0) }}
                                    </div>
                                    <div class="ml-4">
                                        <h3 class="text-lg font-medium text-gray-900">{{ freelancer.name }}</h3>
                                        <p class="text-sm text-gray-500">{{ freelancer.title }}</p>
                                    </div>
                                </div>
                                <div class="mt-4">
                                    <div class="flex items-center">
                                        <div class="flex text-yellow-400">
                                            <svg v-for="i in 5" :key="i" class="w-5 h-5" :class="{ 'text-gray-300': i > freelancer.rating }" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                                                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
                                            </svg>
                                        </div>
                                        <span class="ml-2 text-sm text-gray-500">{{ freelancer.rating }} ({{ freelancer.reviewCount }} reviews)</span>
                                    </div>
                                </div>
                                <div class="mt-4">
                                    <div class="flex flex-wrap gap-2">
                                        <span v-for="(skill, index) in freelancer.skills" :key="index" class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-indigo-100 text-indigo-800">
                                            {{ skill }}
                                        </span>
                                    </div>
                                </div>
                                <div class="mt-6 flex justify-between items-center">
                                    <span class="text-sm font-medium text-gray-900">${{ freelancer.hourlyRate }}/hr</span>
                                    <button class="inline-flex items-center px-3 py-1.5 border border-transparent text-xs font-medium rounded-md text-indigo-700 bg-indigo-100 hover:bg-indigo-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="viewFreelancerProfile(freelancer.id)">
                                        View Profile
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="mt-10 text-center">
                        <button class="inline-flex items-center px-4 py-2 border border-transparent text-base font-medium rounded-md text-indigo-700 bg-indigo-100 hover:bg-indigo-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="setActivePage('freelancers')">
                            View All Freelancers
                        </button>
                    </div>
                </div>
            </div>

            <!-- Projects Page -->
            <div v-if="activePage === 'projects'" class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
                <div class="flex flex-col md:flex-row md:items-center md:justify-between mb-8">
                    <div>
                        <h1 class="text-3xl font-bold text-gray-900">Browse Projects</h1>
                        <p class="mt-1 text-sm text-gray-500">Find projects that match your skills and expertise</p>
                    </div>
                    <div class="mt-4 md:mt-0">
                        <button class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openPostProjectModal">
                            Post a Project
                        </button>
                    </div>
                </div>

                <!-- Filters -->
                <div class="bg-white shadow rounded-lg mb-8 p-4">
                    <div class="grid grid-cols-1 gap-4 md:grid-cols-3">
                        <div>
                            <label for="category" class="block text-sm font-medium text-gray-700">Category</label>
                            <select id="category" v-model="filters.category" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md">
                                <option value="">All Categories</option>
                                <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
                            </select>
                        </div>
                        <div>
                            <label for="budget" class="block text-sm font-medium text-gray-700">Budget</label>
                            <select id="budget" v-model="filters.budget" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md">
                                <option value="">Any Budget</option>
                                <option value="0-100">$0 - $100</option>
                                <option value="100-500">$100 - $500</option>
                                <option value="500-1000">$500 - $1,000</option>
                                <option value="1000+">$1,000+</option>
                            </select>
                        </div>
                        <div>
                            <label for="sort" class="block text-sm font-medium text-gray-700">Sort By</label>
                            <select id="sort" v-model="filters.sort" class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm rounded-md">
                                <option value="newest">Newest First</option>
                                <option value="budget-high">Budget: High to Low</option>
                                <option value="budget-low">Budget: Low to High</option>
                                <option value="bids">Most Bids</option>
                            </select>
                        </div>
                    </div>
                </div>

                <!-- Project List -->
                <div class="space-y-6">
                    <div v-for="project in filteredProjects" :key="project.id" class="bg-white shadow rounded-lg overflow-hidden hover:shadow-md transition-shadow duration-300">
                        <div class="p-6">
                            <div class="flex items-center justify-between">
                                <h2 class="text-xl font-semibold text-gray-900">
                                    <a href="#" class="hover:text-indigo-600" @click="viewProjectDetails(project.id)">{{ project.title }}</a>
                                </h2>
                                <div class="text-sm font-medium text-gray-500">
                                    Posted {{ project.postedDate }}
                                </div>
                            </div>
                            <div class="mt-2">
                                <p class="text-gray-600 line-clamp-3">{{ project.description }}</p>
                            </div>
                            <div class="mt-4 flex flex-wrap gap-2">
                                <span v-for="(skill, index) in project.skills" :key="index" class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-indigo-100 text-indigo-800">
                                    {{ skill }}
                                </span>
                            </div>
                            <div class="mt-6 flex items-center justify-between">
                                <div>
                                    <div class="flex items-center">
                                        <div class="flex-shrink-0">
                                            <div class="h-8 w-8 rounded-full bg-gray-200 flex items-center justify-center text-gray-600 font-semibold">
                                                {{ project.client.name.charAt(0) }}
                                            </div>
                                        </div>
                                        <div class="ml-3">
                                            <p class="text-sm font-medium text-gray-900">{{ project.client.name }}</p>
                                            <div class="flex items-center text-sm text-gray-500">
                                                <svg class="flex-shrink-0 mr-1.5 h-4 w-4 text-yellow-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                                    <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
                                                </svg>
                                                {{ project.client.rating }} ({{ project.client.reviewCount }})
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="flex items-center space-x-4">
                                    <div class="text-right">
                                        <p class="text-sm font-medium text-gray-900">${{ project.budget }}</p>
                                        <p class="text-sm text-gray-500">{{ project.bidCount }} bids</p>
                                    </div>
                                    <button class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openBidModal(project)">
                                        Place Bid
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Pagination -->
                <div class="mt-8 flex justify-center">
                    <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
                        <a href="#" class="relative inline-flex items-center px-2 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
                            <span class="sr-only">Previous</span>
                            <svg class="h-5 w-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                <path fill-rule="evenodd" d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z" clip-rule="evenodd" />
                            </svg>
                        </a>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
                            1
                        </a>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-indigo-50 text-sm font-medium text-indigo-600 hover:bg-indigo-100">
                            2
                        </a>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
                            3
                        </a>
                        <span class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700">
                            ...
                        </span>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
                            8
                        </a>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
                            9
                        </a>
                        <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50">
                            10
                        </a>
                        <a href="#" class="relative inline-flex items-center px-2 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
                            <span class="sr-only">Next</span>
                            <svg class="h-5 w-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                <path fill-rule="evenodd" d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd" />
                            </svg>
                        </a>
                    </nav>
                </div>
            </div>

            <!-- Project Details Page -->
            <div v-if="activePage === 'project-details' && selectedProject" class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
                <div class="mb-6">
                    <button class="inline-flex items-center text-sm text-indigo-600 hover:text-indigo-900" @click="setActivePage('projects')">
                        <svg class="mr-2 h-5 w-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                            <path fill-rule="evenodd" d="M9.707 16.707a1 1 0 01-1.414 0l-6-6a1 1 0 010-1.414l6-6a1 1 0 011.414 1.414L5.414 9H17a1 1 0 110 2H5.414l4.293 4.293a1 1 0 010 1.414z" clip-rule="evenodd" />
                        </svg>
                        Back to Projects
                    </button>
                </div>

                <div class="bg-white shadow rounded-lg overflow-hidden">
                    <div class="p-6">
                        <div class="flex flex-col md:flex-row md:items-center md:justify-between">
                            <h1 class="text-2xl font-bold text-gray-900">{{ selectedProject.title }}</h1>
                            <div class="mt-2 md:mt-0 flex items-center">
                                <span class="inline-flex items-center px-3 py-0.5 rounded-full text-sm font-medium bg-green-100 text-green-800">
                                    {{ selectedProject.status }}
                                </span>
                                <span class="ml-4 text-sm text-gray-500">Posted {{ selectedProject.postedDate }}</span>
                            </div>
                        </div>

                        <div class="mt-6">
                            <h2 class="text-lg font-medium text-gray-900">Project Description</h2>
                            <div class="mt-2 text-gray-600 space-y-4">
                                <p>{{ selectedProject.description }}</p>
                            </div>
                        </div>

                        <div class="mt-6">
                            <h2 class="text-lg font-medium text-gray-900">Skills Required</h2>
                            <div class="mt-2 flex flex-wrap gap-2">
                                <span v-for="(skill, index) in selectedProject.skills" :key="index" class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-indigo-100 text-indigo-800">
                                    {{ skill }}
                                </span>
                            </div>
                        </div>

                        <div class="mt-6 grid grid-cols-1 gap-6 sm:grid-cols-2">
                            <div>
                                <h2 class="text-lg font-medium text-gray-900">Project Details</h2>
                                <dl class="mt-2 text-sm text-gray-600">
                                    <div class="mt-1 flex justify-between">
                                        <dt class="font-medium text-gray-500">Budget</dt>
                                        <dd>${{ selectedProject.budget }}</dd>
                                    </div>
                                    <div class="mt-1 flex justify-between">
                                        <dt class="font-medium text-gray-500">Duration</dt>
                                        <dd>{{ selectedProject.duration }}</dd>
                                    </div>
                                    <div class="mt-1 flex justify-between">
                                        <dt class="font-medium text-gray-500">Bids</dt>
                                        <dd>{{ selectedProject.bidCount }} proposals</dd>
                                    </div>
                                    <div class="mt-1 flex justify-between">
                                        <dt class="font-medium text-gray-500">Deadline</dt>
                                        <dd>{{ selectedProject.deadline }}</dd>
                                    </div>
                                </dl>
                            </div>
                            <div>
                                <h2 class="text-lg font-medium text-gray-900">Client Information</h2>
                                <div class="mt-2 flex items-center">
                                    <div class="flex-shrink-0">
                                        <div class="h-10 w-10 rounded-full bg-gray-200 flex items-center justify-center text-gray-600 font-semibold">
                                            {{ selectedProject.client.name.charAt(0) }}
                                        </div>
                                    </div>
                                    <div class="ml-3">
                                        <p class="text-sm font-medium text-gray-900">{{ selectedProject.client.name }}</p>
                                        <div class="flex items-center text-sm text-gray-500">
                                            <svg class="flex-shrink-0 mr-1.5 h-4 w-4 text-yellow-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
                                            </svg>
                                            {{ selectedProject.client.rating }} ({{ selectedProject.client.reviewCount }})
                                        </div>
                                        <p class="text-sm text-gray-500">Member since {{ selectedProject.client.memberSince }}</p>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="mt-8 flex flex-col sm:flex-row sm:justify-end">
                            <button class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openBidModal(selectedProject)">
                                Place Bid
                            </button>
                            <button class="mt-3 sm:mt-0 sm:ml-3 inline-flex items-center px-4 py-2 border border-gray-300 shadow-sm text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="saveProject(selectedProject.id)">
                                Save Project
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Bids Section -->
                <div class="mt-8">
                    <h2 class="text-xl font-bold text-gray-900 mb-4">Proposals ({{ selectedProject.bids.length }})</h2>
                    <div class="space-y-4">
                        <div v-for="bid in selectedProject.bids" :key="bid.id" class="bg-white shadow rounded-lg overflow-hidden">
                            <div class="p-6">
                                <div class="flex items-center justify-between">
                                    <div class="flex items-center">
                                        <div class="flex-shrink-0">
                                            <div class="h-10 w-10 rounded-full bg-indigo-100 flex items-center justify-center text-indigo-800 font-semibold">
                                                {{ bid.freelancer.name.charAt(0) }}
                                            </div>
                                        </div>
                                        <div class="ml-3">
                                            <p class="text-sm font-medium text-gray-900">{{ bid.freelancer.name }}</p>
                                            <div class="flex items-center text-sm text-gray-500">
                                                <svg class="flex-shrink-0 mr-1.5 h-4 w-4 text-yellow-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                                    <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
                                                </svg>
                                                {{ bid.freelancer.rating }} ({{ bid.freelancer.reviewCount }})
                                            </div>
                                        </div>
                                    </div>
                                    <div class="text-right">
                                        <p class="text-sm font-medium text-gray-900">${{ bid.amount }}</p>
                                        <p class="text-sm text-gray-500">{{ bid.deliveryTime }}</p>
                                    </div>
                                </div>
                                <div class="mt-4">
                                    <p class="text-sm text-gray-600">{{ bid.proposal }}</p>
                                </div>
                                <div class="mt-4 flex justify-end space-x-3">
                                    <button class="inline-flex items-center px-3 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openMessageModal(bid.freelancer)">
                                        Message
                                    </button>
                                    <button v-if="currentUser.role === 'client'" class="inline-flex items-center px-3 py-1.5 border border-transparent shadow-sm text-xs font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="acceptBid(bid)">
                                        Accept Bid
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Dashboard Page -->
            <div v-if="activePage === 'dashboard'" class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
                <h1 class="text-3xl font-bold text-gray-900 mb-8">Dashboard</h1>

                <!-- Dashboard Tabs -->
                <div class="border-b border-gray-200">
                    <nav class="-mb-px flex space-x-8">
                        <a href="#" class="py-4 px-1 border-b-2 font-medium text-sm" :class="dashboardTab === 'overview' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="dashboardTab = 'overview'">
                            Overview
                        </a>
                        <a href="#" class="py-4 px-1 border-b-2 font-medium text-sm" :class="dashboardTab === 'projects' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="dashboardTab = 'projects'">
                            {{ currentUser.role === 'client' ? 'My Projects' : 'My Bids' }}
                        </a>
                        <a href="#" class="py-4 px-1 border-b-2 font-medium text-sm" :class="dashboardTab === 'contracts' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="dashboardTab = 'contracts'">
                            Contracts
                        </a>
                        <a href="#" class="py-4 px-1 border-b-2 font-medium text-sm" :class="dashboardTab === 'messages' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="dashboardTab = 'messages'">
                            Messages
                        </a>
                        <a href="#" class="py-4 px-1 border-b-2 font-medium text-sm" :class="dashboardTab === 'payments' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="dashboardTab = 'payments'">
                            Payments
                        </a>
                    </nav>
                </div>

                <!-- Overview Tab -->
                <div v-if="dashboardTab === 'overview'" class="mt-8">
                    <div class="grid grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-4">
                        <div class="bg-white overflow-hidden shadow rounded-lg">
                            <div class="px-4 py-5 sm:p-6">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0 bg-indigo-500 rounded-md p-3">
                                        <svg class="h-6 w-6 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                                        </svg>
                                    </div>
                                    <div class="ml-5 w-0 flex-1">
                                        <dl>
                                            <dt class="text-sm font-medium text-gray-500 truncate">
                                                {{ currentUser.role === 'client' ? 'Active Projects' : 'Active Bids' }}
                                            </dt>
                                            <dd>
                                                <div class="text-lg font-medium text-gray-900">
                                                    {{ currentUser.role === 'client' ? '4' : '12' }}
                                                </div>
                                            </dd>
                                        </dl>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="bg-white overflow-hidden shadow rounded-lg">
                            <div class="px-4 py-5 sm:p-6">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0 bg-green-500 rounded-md p-3">
                                        <svg class="h-6 w-6 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                                        </svg>
                                    </div>
                                    <div class="ml-5 w-0 flex-1">
                                        <dl>
                                            <dt class="text-sm font-medium text-gray-500 truncate">
                                                Completed Projects
                                            </dt>
                                            <dd>
                                                <div class="text-lg font-medium text-gray-900">
                                                    {{ currentUser.role === 'client' ? '8' : '23' }}
                                                </div>
                                            </dd>
                                        </dl>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="bg-white overflow-hidden shadow rounded-lg">
                            <div class="px-4 py-5 sm:p-6">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0 bg-yellow-500 rounded-md p-3">
                                        <svg class="h-6 w-6 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                                        </svg>
                                    </div>
                                    <div class="ml-5 w-0 flex-1">
                                        <dl>
                                            <dt class="text-sm font-medium text-gray-500 truncate">
                                                Pending Reviews
                                            </dt>
                                            <dd>
                                                <div class="text-lg font-medium text-gray-900">
                                                    2
                                                </div>
                                            </dd>
                                        </dl>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="bg-white overflow-hidden shadow rounded-lg">
                            <div class="px-4 py-5 sm:p-6">
                                <div class="flex items-center">
                                    <div class="flex-shrink-0 bg-purple-500 rounded-md p-3">
                                        <svg class="h-6 w-6 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                                        </svg>
                                    </div>
                                    <div class="ml-5 w-0 flex-1">
                                        <dl>
                                            <dt class="text-sm font-medium text-gray-500 truncate">
                                                {{ currentUser.role === 'client' ? 'Total Spent' : 'Total Earned' }}
                                            </dt>
                                            <dd>
                                                <div class="text-lg font-medium text-gray-900">
                                                    ${{ currentUser.role === 'client' ? '4,250' : '6,840' }}
                                                </div>
                                            </dd>
                                        </dl>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Recent Activity -->
                    <div class="mt-8">
                        <h2 class="text-lg font-medium text-gray-900">Recent Activity</h2>
                        <div class="mt-4 bg-white shadow rounded-lg overflow-hidden">
                            <ul class="divide-y divide-gray-200">
                                <li v-for="(activity, index) in recentActivity" :key="index" class="px-6 py-4">
                                    <div class="flex items-center">
                                        <div class="flex-shrink-0">
                                            <div :class="`h-8 w-8 rounded-full flex items-center justify-center ${activity.iconBg}`">
                                                <svg class="h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                                    <path v-if="activity.type === 'message'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z" />
                                                    <path v-else-if="activity.type === 'bid'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                                                    <path v-else-if="activity.type === 'payment'" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                                                    <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                                                </svg>
                                            </div>
                                        </div>
                                        <div class="ml-4">
                                            <p class="text-sm font-medium text-gray-900">{{ activity.message }}</p>
                                            <p class="text-sm text-gray-500">{{ activity.time }}</p>
                                        </div>
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>

                <!-- Projects/Bids Tab -->
                <div v-if="dashboardTab === 'projects'" class="mt-8">
                    <div class="flex flex-col md:flex-row md:items-center md:justify-between mb-6">
                        <h2 class="text-lg font-medium text-gray-900">{{ currentUser.role === 'client' ? 'My Projects' : 'My Bids' }}</h2>
                        <div class="mt-3 md:mt-0">
                            <button v-if="currentUser.role === 'client'" class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openPostProjectModal">
                                Post New Project
                            </button>
                        </div>
                    </div>

                    <!-- Client Projects -->
                    <div v-if="currentUser.role === 'client'" class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="border-b border-gray-200">
                            <nav class="-mb-px flex">
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="projectsFilter === 'active' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="projectsFilter = 'active'">
                                    Active (4)
                                </a>
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="projectsFilter === 'completed' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="projectsFilter = 'completed'">
                                    Completed (8)
                                </a>
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="projectsFilter === 'draft' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="projectsFilter = 'draft'">
                                    Draft (2)
                                </a>
                            </nav>
                        </div>
                        <ul class="divide-y divide-gray-200">
                            <li v-for="project in filteredDashboardProjects" :key="project.id" class="px-6 py-5">
                                <div class="flex items-center justify-between">
                                    <div class="flex-1 min-w-0">
                                        <a href="#" class="text-lg font-medium text-indigo-600 hover:text-indigo-900 truncate" @click="viewProjectDetails(project.id)">
                                            {{ project.title }}
                                        </a>
                                        <div class="mt-1 flex items-center">
                                            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium" :class="getStatusClass(project.status)">
                                                {{ project.status }}
                                            </span>
                                            <span class="ml-2 text-sm text-gray-500">{{ project.bidCount }} bids</span>
                                            <span class="mx-2 text-gray-500">•</span>
                                            <span class="text-sm text-gray-500">Posted {{ project.postedDate }}</span>
                                        </div>
                                    </div>
                                    <div class="ml-4 flex-shrink-0 flex">
                                        <span class="inline-block text-lg font-medium text-gray-900">${{ project.budget }}</span>
                                    </div>
                                </div>
                                <div class="mt-4 flex justify-end space-x-3">
                                    <button class="inline-flex items-center px-3 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="viewProjectDetails(project.id)">
                                        View Details
                                    </button>
                                    <button class="inline-flex items-center px-3 py-1.5 border border-transparent shadow-sm text-xs font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="editProject(project.id)">
                                        Edit Project
                                    </button>
                                </div>
                            </li>
                        </ul>
                    </div>

                    <!-- Freelancer Bids -->
                    <div v-if="currentUser.role === 'freelancer'" class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="border-b border-gray-200">
                            <nav class="-mb-px flex">
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="bidsFilter === 'active' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="bidsFilter = 'active'">
                                    Active (12)
                                </a>
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="bidsFilter === 'accepted' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="bidsFilter = 'accepted'">
                                    Accepted (5)
                                </a>
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="bidsFilter === 'archived' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="bidsFilter = 'archived'">
                                    Archived (8)
                                </a>
                            </nav>
                        </div>
                        <ul class="divide-y divide-gray-200">
                            <li v-for="bid in filteredBids" :key="bid.id" class="px-6 py-5">
                                <div class="flex items-center justify-between">
                                    <div class="flex-1 min-w-0">
                                        <a href="#" class="text-lg font-medium text-indigo-600 hover:text-indigo-900 truncate" @click="viewProjectDetails(bid.project.id)">
                                            {{ bid.project.title }}
                                        </a>
                                        <div class="mt-1 flex items-center">
                                            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium" :class="getStatusClass(bid.status)">
                                                {{ bid.status }}
                                            </span>
                                            <span class="ml-2 text-sm text-gray-500">Bid: ${{ bid.amount }}</span>
                                            <span class="mx-2 text-gray-500">•</span>
                                            <span class="text-sm text-gray-500">Submitted {{ bid.submittedDate }}</span>
                                        </div>
                                    </div>
                                </div>
                                <div class="mt-4 flex justify-end space-x-3">
                                    <button class="inline-flex items-center px-3 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="viewProjectDetails(bid.project.id)">
                                        View Project
                                    </button>
                                    <button class="inline-flex items-center px-3 py-1.5 border border-transparent shadow-sm text-xs font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="editBid(bid.id)">
                                        Edit Bid
                                    </button>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Contracts Tab -->
                <div v-if="dashboardTab === 'contracts'" class="mt-8">
                    <h2 class="text-lg font-medium text-gray-900 mb-6">Contracts</h2>
                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="border-b border-gray-200">
                            <nav class="-mb-px flex">
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="contractsFilter === 'active' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="contractsFilter = 'active'">
                                    Active (3)
                                </a>
                                <a href="#" class="py-4 px-6 border-b-2 font-medium text-sm" :class="contractsFilter === 'completed' ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'" @click="contractsFilter = 'completed'">
                                    Completed (5)
                                </a>
                            </nav>
                        </div>
                        <ul class="divide-y divide-gray-200">
                            <li v-for="contract in filteredContracts" :key="contract.id" class="px-6 py-5">
                                <div class="flex items-center justify-between">
                                    <div class="flex-1 min-w-0">
                                        <a href="#" class="text-lg font-medium text-indigo-600 hover:text-indigo-900 truncate" @click="viewContractDetails(contract.id)">
                                            {{ contract.title }}
                                        </a>
                                        <div class="mt-1 flex items-center">
                                            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium" :class="getStatusClass(contract.status)">
                                                {{ contract.status }}
                                            </span>
                                            <span class="mx-2 text-gray-500">•</span>
                                            <span class="text-sm text-gray-500">Started {{ contract.startDate }}</span>
                                            <span class="mx-2 text-gray-500">•</span>
                                            <span class="text-sm text-gray-500">Due {{ contract.endDate }}</span>
                                        </div>
                                    </div>
                                    <div class="ml-4 flex-shrink-0 flex">
                                        <span class="inline-block text-lg font-medium text-gray-900">${{ contract.amount }}</span>
                                    </div>
                                </div>
                                <div class="mt-4">
                                    <div class="relative pt-1">
                                        <div class="flex mb-2 items-center justify-between">
                                            <div>
                                                <span class="text-xs font-semibold inline-block text-indigo-600">
                                                    Progress
                                                </span>
                                            </div>
                                            <div class="text-right">
                                                <span class="text-xs font-semibold inline-block text-indigo-600">
                                                    {{ contract.progress }}%
                                                </span>
                                            </div>
                                        </div>
                                        <div class="overflow-hidden h-2 mb-4 text-xs flex rounded bg-indigo-200">
                                            <div :style="`width: ${contract.progress}%`" class="shadow-none flex flex-col text-center whitespace-nowrap text-white justify-center bg-indigo-500 progress-bar"></div>
                                        </div>
                                    </div>
                                </div>
                                <div class="mt-4 flex justify-end space-x-3">
                                    <button class="inline-flex items-center px-3 py-1.5 border border-gray-300 shadow-sm text-xs font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="openMessageModal(contract.otherParty)">
                                        Message
                                    </button>
                                    <button class="inline-flex items-center px-3 py-1.5 border border-transparent shadow-sm text-xs font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="viewContractDetails(contract.id)">
                                        View Details
                                    </button>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Messages Tab -->
                <div v-if="dashboardTab === 'messages'" class="mt-8">
                    <h2 class="text-lg font-medium text-gray-900 mb-6">Messages</h2>
                    <div class="bg-white shadow rounded-lg overflow-hidden">
                        <div class="grid grid-cols-1 md:grid-cols-3">
                            <!-- Conversation List -->
                            <div class="border-r border-gray-200">
                                <div class="p-4 border-b border-gray-200">
                                    <div class="relative">
                                        <input type="text" placeholder="Search messages" class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md leading-5 bg-white placeholder-gray-500 focus:outline-none focus:placeholder-gray-400 focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm">
                                        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                            <svg class="h-5 w-5 text-gray-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                                <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4```
