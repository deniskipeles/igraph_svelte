<script>
	import { app } from '$lib/store/firebase'
	import { user,userProfileData } from '$lib/store/user'
	import { doc, setDoc } from "firebase/firestore"; 
	import { getFirestore,serverTimestamp,collection, addDoc } from "firebase/firestore";


	let openImageDialogBox = false;
	let isLoading = false;
	let postImage = "";
	let postText = "";

	 const handlePostUpload = async () => {
	 	isLoading = true;
	 	if($user){
			const db = getFirestore(app);
	 		if(postImage.trim() != "" && postText.trim() === ""){
	 			try{
					const docRef = await addDoc(collection(db, "users", $user?.uid, "posts"), {
					  image: postImage,
					  text: null,
					  uploadTime: serverTimestamp(),
					  $userProfileData
					})
					await setDoc(doc(db, "users", $user?.uid, "posts", docRef?.id), {
					  image: postImage,
					  text: null,
					  postId: docRef?.id,
					  uploadTime: serverTimestamp(),
					},{ merge: true });
	 			}catch(error){
	 				isLoading = false;
	 			}finally{
	 				isLoading = false;
	 				postImage = "";
	 			}
	 		}
	 		if(postText.trim() != "" && postImage.trim() === ""){
	 			try{
					const docRef = await addDoc(collection(db, "users", $user?.uid, "posts"), {
					  image: null,
					  text: postText,
					  uploadTime: serverTimestamp(),
						$userProfileData
					})
					await setDoc(doc(db, "users", $user?.uid, "posts", docRef?.id), {
					  image: null,
					  text: postText,
					  postId: docRef?.id,
					  uploadTime: serverTimestamp(),
					},{ merge: true });
	 			}catch(error){
	 				isLoading = false;
	 			}finally{
	 				isLoading = false;
	 				postText = "";
	 			}
	 		}
	 		if(postText.trim() != "" && postImage.trim() != ""){
	 			console.log("both",postText,postImage)
	 			try{
					const docRef = await addDoc(collection(db, "users", $user?.uid, "posts"), {
					  image: postImage,
					  text: postText,
					  uploadTime: serverTimestamp(),
					  $userProfileData
					})
					await setDoc(doc(db, "users", $user?.uid, "posts", docRef?.id), {
					  image: postImage,
					  text: postText,
					  postId: docRef?.id,
					  uploadTime: serverTimestamp(),
					},{ merge: true });
	 			}catch(error){
	 				isLoading = false;
	 			}finally{
	 				isLoading = false;
	 				postText = "";
	 				postImage = "";
	 			}
	 		}
	 	}else{
	 		isLoading = false
	 		return;
	 	}
	 }
</script>

<dialog open={openImageDialogBox} class="border border-gray-600 bg-[#242526] text-gray-100 shadow-lg rounded-lg w-[20rem] md:w-[30rem] lg:w-[36rem] mx-auto px-4">
	<div class="flex flex-col items-start space-y-2 w-full">
		<div class="w-full flex flex-col space-y-2">		
			<label for="name" class="text-sm font-medium text-gray-400">
					<span>Upload Image:</span>
					<small class="italic text-[0.7rem]">(Enter URL of the image)</small>
			</label>
			<input bind:value={postImage} id="name" class="focus:outline-none border border-gray-600 bg-transparent w-full py-1.5 px-3 rounded-lg " type="url" placeholder="Image URL">
		</div>	
		<div class="flex items-center justify-center w-full">
			{#if postImage}
				<img class="w-full h-full object-cover" src={postImage} alt="Image Url">
			{/if}
		</div>
		<div class="text-sm flex items-center justify-end w-full space-x-2 pt-3">
			<button on:click={() => openImageDialogBox = !openImageDialogBox} class="py-1.5 px-3 rounded-md text-red-500">Cancel</button>
			<button on:click={() => openImageDialogBox = !openImageDialogBox} class="border border-gray-600 hover:border-[#1877F2] py-1.5 px-3 rounded-md hover:bg-[#1877F2] hover:text-gray-100">Upload</button>
		</div>
	</div>
</dialog>
<div class="px-4 py-3 pb-2 bg-[#242526] rounded-md mt-4 lg:mt-6 w-full container mx-auto max-w-lg">
	<div class="flex flex-col space-y-1">
		<div class="flex items-center space-x-2">
			<div class="w-10 h-10">
				<img class="w-full h-full object-cover rounded-full" src={postImage || $userProfileData?.photo} alt="img">
			</div>
			<input bind:value={postText} class="focus:outline-none w-full flex-1 py-2.5 px-4 rounded-full bg-[#3a3b3c]" type="text" placeholder="Whats on Your mind ?">
			<button on:click={handlePostUpload} disabled={isLoading} title="Upload Post" class="flex items-center bg-[#1877F2] w-10 h-10 grid place-items-center rounded-full">
				{#if isLoading}
					<svg class="animate-spin h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
			          <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
			          <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
			        </svg>
				{:else}
					<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-5 h-5"><path d="M9.912 12H4L2.023 4.135A.662.662 0 0 1 2 3.995c-.022-.721.772-1.221 1.46-.891L22 12 3.46 20.896c-.68.327-1.464-.159-1.46-.867a.66.66 0 0 1 .033-.186L3.5 15"/></svg>
				{/if}
			</button>
		</div>
		<div class="py-3 pb-2">
			<div class="w-full h-0.5 bg-gray-700"></div>
		</div>
		<div class="flex items-center justify-between">
			<div class="w-full text-[#b0b3b8] flex items-center space-x-2 justify-center py-2 rounded-md px-3 font-medium hover:bg-[#3a3b3c] cursor-pointer">
				<svg viewBox="0 0 24 24" width="1.5rem" height="1.5rem" class="fill-[#f3425f]"><g fill-rule="evenodd" transform="translate(-444 -156)"><g><path d="M113.029 2.514c-.363-.088-.746.014-1.048.234l-2.57 1.88a.999.999 0 0 0-.411.807v8.13a1 1 0 0 0 .41.808l2.602 1.901c.219.16.477.242.737.242.253 0 .508-.077.732-.235.34-.239.519-.65.519-1.065V3.735a1.25 1.25 0 0 0-.971-1.22m-20.15 6.563c.1-.146 2.475-3.578 5.87-3.578 3.396 0 5.771 3.432 5.87 3.578a.749.749 0 0 1 0 .844c-.099.146-2.474 3.578-5.87 3.578-3.395 0-5.77-3.432-5.87-3.578a.749.749 0 0 1 0-.844zM103.75 19a3.754 3.754 0 0 0 3.75-3.75V3.75A3.754 3.754 0 0 0 103.75 0h-10A3.754 3.754 0 0 0 90 3.75v11.5A3.754 3.754 0 0 0 93.75 19h10z" transform="translate(354 158.5)"></path><path d="M98.75 12c1.379 0 2.5-1.121 2.5-2.5S100.129 7 98.75 7a2.503 2.503 0 0 0-2.5 2.5c0 1.379 1.121 2.5 2.5 2.5" transform="translate(354 158.5)"></path></g></g></svg>
				<span class="hidden lg:inline">Live Video</span>
			</div>
			<div on:click={() => openImageDialogBox = !openImageDialogBox} class="w-full text-[#b0b3b8] flex items-center space-x-2 justify-center py-2 rounded-md px-3 font-medium hover:bg-[#3a3b3c] cursor-pointer">
				<svg viewBox="0 0 24 24" width="1.5rem" height="1.5rem" class="fill-[#45bd62]"><g fill-rule="evenodd" transform="translate(-444 -156)"><g><path d="m96.968 22.425-.648.057a2.692 2.692 0 0 1-1.978-.625 2.69 2.69 0 0 1-.96-1.84L92.01 4.32a2.702 2.702 0 0 1 .79-2.156c.47-.472 1.111-.731 1.774-.79l2.58-.225a.498.498 0 0 1 .507.675 4.189 4.189 0 0 0-.251 1.11L96.017 18.85a4.206 4.206 0 0 0 .977 3.091s.459.364-.026.485m8.524-16.327a1.75 1.75 0 1 1-3.485.305 1.75 1.75 0 0 1 3.485-.305m5.85 3.011a.797.797 0 0 0-1.129-.093l-3.733 3.195a.545.545 0 0 0-.062.765l.837.993a.75.75 0 1 1-1.147.966l-2.502-2.981a.797.797 0 0 0-1.096-.12L99 14.5l-.5 4.25c-.06.674.326 2.19 1 2.25l11.916 1.166c.325.026 1-.039 1.25-.25.252-.21.89-.842.917-1.166l.833-8.084-3.073-3.557z" transform="translate(352 156.5)"></path><path fill-rule="nonzero" d="m111.61 22.963-11.604-1.015a2.77 2.77 0 0 1-2.512-2.995L98.88 3.09A2.77 2.77 0 0 1 101.876.58l11.603 1.015a2.77 2.77 0 0 1 2.513 2.994l-1.388 15.862a2.77 2.77 0 0 1-2.994 2.513zm.13-1.494.082.004a1.27 1.27 0 0 0 1.287-1.154l1.388-15.862a1.27 1.27 0 0 0-1.148-1.37l-11.604-1.014a1.27 1.27 0 0 0-1.37 1.15l-1.387 15.86a1.27 1.27 0 0 0 1.149 1.37l11.603 1.016z" transform="translate(352 156.5)"></path></g></g></svg>
				<span class="hidden lg:inline">Photos/Videos</span>
			</div>
			<div class="w-full text-[#b0b3b8] flex items-center space-x-2 justify-center py-2 rounded-md px-3 font-medium hover:bg-[#3a3b3c] cursor-pointer">
				<svg viewBox="0 0 24 24" width="1.5rem" height="1.5rem" class="fill-[#f7b928]"><g fill-rule="evenodd" transform="translate(-444 -156)"><g><path d="M107.285 13c.49 0 .841.476.712.957-.623 2.324-2.837 4.043-5.473 4.043-2.636 0-4.85-1.719-5.473-4.043-.13-.48.222-.957.712-.957h9.522z" transform="translate(353.5 156.5)"></path><path fill-rule="nonzero" d="M114.024 11.5c0 6.351-5.149 11.5-11.5 11.5s-11.5-5.149-11.5-11.5S96.173 0 102.524 0s11.5 5.149 11.5 11.5zm-2 0a9.5 9.5 0 1 0-19 0 9.5 9.5 0 0 0 19 0z" transform="translate(353.5 156.5)"></path><path d="M99.524 8.5c0 .829-.56 1.5-1.25 1.5s-1.25-.671-1.25-1.5.56-1.5 1.25-1.5 1.25.671 1.25 1.5m8.5 0c0 .829-.56 1.5-1.25 1.5s-1.25-.671-1.25-1.5.56-1.5 1.25-1.5 1.25.671 1.25 1.5m-.739 4.5h-9.522c-.49 0-.841.476-.712.957.623 2.324 2.837 4.043 5.473 4.043 2.636 0 4.85-1.719 5.473-4.043.13-.48-.222-.957-.712-.957m-2.165 2c-.667.624-1.592 1-2.596 1a3.799 3.799 0 0 1-2.596-1h5.192" transform="translate(353.5 156.5)"></path></g></g></svg>
				<span class="hidden lg:inline">Feeling/Activity</span>
			</div>
		</div>
	</div>
</div>