<style>
@keyframes neon {
  0%, 100% { text-shadow: 0 0 10px #8b5cf6, 0 0 20px #7c3aed, 0 0 40px #6d28d9, 0 0 80px #5b21b6; }
  50% { text-shadow: 0 0 20px #3b82f6, 0 0 40px #2563eb, 0 0 80px #1d4ed8, 0 0 120px #1e40af; }
}
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.title {
  font-size: 52px; font-weight: 900; text-align: center;
  color: #c4b5fd;
  animation: neon 2s ease-in-out infinite;
  margin: 30px 0 0 0;
}
.line {
  height: 3px; margin: 20px auto 12px; max-width: 200px;
  background: linear-gradient(90deg, #8b5cf6, #3b82f6, #06b6d4);
  background-size: 200% 100%;
  animation: shimmer 2s linear infinite;
  border-radius: 2px;
}
.sub {
  text-align: center; color: #64748b; font-size: 14px; letter-spacing: 4px;
}
</style>

<p class="title"><a href="/" style="text-decoration: none; color: inherit;">宵咕咕啦</a></p>
<div class="line"></div>
<p class="sub">✦ ✦ ✦</p>
