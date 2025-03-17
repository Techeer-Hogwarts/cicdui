<script lang="ts">
    import { goto } from '$app/navigation';
    
    let currentPassword = '';
    let newPassword = '';
    let confirmPassword = '';
    let loading = false;
    let message = '';
    let isSuccess = false;
    
    // Client-side email validation
    function isValidEmail(email: string): boolean {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }
    
    async function handleSubmit(event: SubmitEvent) {
      event.preventDefault();
      message = '';
      isSuccess = false;
      
      if (newPassword.length < 8) {
        message = 'New password must be at least 8 characters long';
        return;
      }
      
      if (newPassword !== confirmPassword) {
        message = 'New passwords do not match';
        return;
      }
      
      if (currentPassword === newPassword) {
        message = 'New password must be different from current password';
        return;
      }
      
      try {
        loading = true;
        
        // Replace with your API endpoint
        const response = await fetch('https://your-api-server.com/change-password', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ 
            currentPassword, 
            newPassword 
          })
        });
        
        const data = await response.json();
        
        if (!response.ok) {
          throw new Error(data.message || 'Password reset failed');
        }
        
        isSuccess = true;
        message = 'Your password has been changed successfully';
        
        // Clear form fields
        currentPassword = '';
        newPassword = '';
        confirmPassword = '';
        
        // Redirect to login page after a short delay
        setTimeout(() => {
          goto('/login');
        }, 3000);
      } catch (error) {
        isSuccess = false;
        message = error instanceof Error ? error.message : 'An error occurred while changing your password';
      } finally {
        loading = false;
      }
    }
  </script>
  
  <svelte:head>
    <title>Change Password</title>
  </svelte:head>
  
  <div class="min-h-screen flex items-center justify-center bg-gray-50">
    <div class="max-w-md w-full space-y-8 p-8 bg-white rounded-lg shadow">
      <div class="text-center">
        <h1 class="text-3xl font-extrabold text-gray-900">Change Password</h1>
        <p class="mt-2 text-sm text-gray-600">
          Update your password by entering your current and new password
        </p>
      </div>
  
      <form class="mt-8 space-y-6" on:submit={handleSubmit}>
        {#if message}
          <div class={`p-4 rounded-md ${isSuccess ? 'bg-green-50 text-green-800' : 'bg-red-50 text-red-800'}`}>
            {message}
          </div>
        {/if}
  
        <div class="space-y-4">
          <div>
            <label for="current-password" class="block text-sm font-medium text-gray-700">Current password</label>
            <div class="mt-1">
              <input
                id="current-password"
                bind:value={currentPassword}
                type="password"
                autocomplete="current-password"
                required
                class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                placeholder="••••••••"
              />
            </div>
          </div>
          
          <div>
            <label for="new-password" class="block text-sm font-medium text-gray-700">New password</label>
            <div class="mt-1">
              <input
                id="new-password"
                bind:value={newPassword}
                type="password"
                autocomplete="new-password"
                required
                class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                placeholder="••••••••"
              />
            </div>
          </div>
          
          <div>
            <label for="confirm-password" class="block text-sm font-medium text-gray-700">Confirm new password</label>
            <div class="mt-1">
              <input
                id="confirm-password"
                bind:value={confirmPassword}
                type="password"
                autocomplete="new-password"
                required
                class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                placeholder="••••••••"
              />
            </div>
          </div>
        </div>
  
        <div>
          <button
            type="submit"
            class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            disabled={loading}
          >
            {#if loading}
              <span class="absolute left-0 inset-y-0 flex items-center pl-3">
                <svg class="animate-spin h-5 w-5 text-indigo-300" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
              </span>
              Changing password...
            {:else}
              Change password
            {/if}
          </button>
        </div>
  
        <div class="text-center">
          <a href="/login" class="font-medium text-indigo-600 hover:text-indigo-500">
            Back to login
          </a>
        </div>
      </form>
    </div>
  </div>