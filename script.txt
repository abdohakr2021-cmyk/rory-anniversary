const songs = [
  {id:"iWwA7AF7UOY", start:85, end:116},
  {id:"kwV6viNd0mE", start:30, end:86}
];
let ytPlayer = null;
let selectedSong = 0;
let stopTimer = null;
function onYouTubeIframeAPIReady(){
  ytPlayer = new YT.Player("player", {
    videoId: songs[0].id,
    playerVars: {playsinline:1, rel:0},
    events: {onReady: () => {}, onStateChange: onPlayerStateChange}
  });
}
window.onYouTubeIframeAPIReady = onYouTubeIframeAPIReady;
function playSong(index){
  selectedSong = index;
  const s = songs[index];
  if(!ytPlayer || !ytPlayer.loadVideoById){
    document.getElementById("player").innerHTML =
      '<p style="padding:20px;text-align:center">مشغل YouTube بيجهز. جرب تاني بعد لحظة.</p>';
    return;
  }
  clearInterval(stopTimer);
  ytPlayer.loadVideoById({videoId:s.id, startSeconds:s.start, endSeconds:s.end});
  stopTimer = setInterval(() => {
    if(ytPlayer && ytPlayer.getCurrentTime && ytPlayer.getPlayerState() === YT.PlayerState.PLAYING){
      if(ytPlayer.getCurrentTime() >= s.end - 0.25){
        if(index === 0){ playSong(1); }
        else { ytPlayer.pauseVideo(); clearInterval(stopTimer); }
      }
    }
  }, 250);
}
function onPlayerStateChange(event){
  if(event.data === YT.PlayerState.ENDED && selectedSong === 0) playSong(1);
}
document.querySelectorAll(".song-btn").forEach(btn=>{
  btn.addEventListener("click",()=>playSong(Number(btn.dataset.song)));
});
document.getElementById("open-letter").addEventListener("click",()=>{
  const letter = document.getElementById("love-letter");
  const button = document.getElementById("open-letter");
  const opening = letter.hidden;
  letter.hidden = !opening;
  button.setAttribute("aria-expanded", String(opening));
  button.querySelector("span:last-child").textContent = opening ? "اقفلي الرسالة" : "افتحي رسالتك";
  if(opening) letter.scrollIntoView({behavior:"smooth",block:"center"});
});
const heartBox = document.querySelector(".floating-hearts");
for(let i=0;i<14;i++){
  const heart=document.createElement("span");
  heart.textContent = i%3===0 ? "♥" : "♡";
  heart.style.left=(Math.random()*100)+"%";
  heart.style.animationDuration=(12+Math.random()*15)+"s";
  heart.style.animationDelay=(-Math.random()*20)+"s";
  heart.style.fontSize=(12+Math.random()*18)+"px";
  heartBox.appendChild(heart);
}
