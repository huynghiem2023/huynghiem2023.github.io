# Hướng dẫn chơi Rubik 3x3x3

Ứng dụng tương tác 3D và hướng dẫn giải Rubik 3x3x3 theo phương pháp cơ bản (Beginner's Method) cực kỳ dễ hiểu của thầy Huy, giúp các em học sinh dễ dàng làm quen và tự giải được khối Rubik tại nhà.

```{raw} html
<style>
/* Modern premium glassmorphism styling for Rubik tutorial app */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');

.rubik-app-wrapper {
  font-family: 'Outfit', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  color: #f3f4f6;
  background: radial-gradient(circle at top left, #1f1b2e, #111019);
  padding: 24px;
  border-radius: 20px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(255, 255, 255, 0.08);
  margin: 20px 0;
  overflow: hidden;
}

.rubik-app-title {
  text-align: center;
  font-weight: 700;
  font-size: 2rem;
  background: linear-gradient(135deg, #a78bfa, #818cf8);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-bottom: 24px;
  letter-spacing: -0.5px;
}

.cube-workspace {
  display: grid;
  grid-template-columns: 1.2fr 1.8fr;
  gap: 24px;
}

@media (max-width: 900px) {
  .cube-workspace {
    grid-template-columns: 1fr;
  }
}

/* 3D Scene styling */
.cube-left-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: rgba(255, 255, 255, 0.03);
  padding: 20px;
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.scene {
  width: 250px;
  height: 250px;
  perspective: 600px;
  margin: 20px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  user-select: none;
}

/* The actual 3D Rubik's cube container */
.cube {
  position: relative;
  width: 156px;
  height: 156px;
  transform-style: preserve-3d;
  transform: rotateX(-25deg) rotateY(45deg);
  cursor: grab;
  transition: transform 0.1s ease-out;
}

.cube:active {
  cursor: grabbing;
}

/* 3D Cubie pieces */
.cubie {
  position: absolute;
  width: 50px;
  height: 50px;
  left: 53px;
  top: 53px;
  transform-style: preserve-3d;
  transform: translate3d(calc(var(--x) * 52px), calc(var(--y) * 52px), calc(var(--z) * 52px));
}

.cubie.animating {
  transition: transform 0.4s ease-in-out;
}

/* 6 Faces of each cubie */
.face {
  position: absolute;
  width: 48px;
  height: 48px;
  background-color: #121214; /* Realistic black plastic speedcube body */
  border: 1.5px solid #08080a;
  border-radius: 6px;
  box-sizing: border-box;
  display: flex;
  justify-content: center;
  align-items: center;
  backface-visibility: visible;
}

/* Premium beveled stickers */
.face::after {
  content: '';
  width: 39px;
  height: 39px;
  border-radius: 5px;
  box-shadow: inset 0 2px 3px rgba(255, 255, 255, 0.35), inset 0 -2px 3px rgba(0, 0, 0, 0.3);
  transition: background 0.3s ease;
  box-sizing: border-box;
}

/* 3D position transforms for each sticker face */
.face.u { transform: rotateX(90deg) translateZ(25px); }
.face.d { transform: rotateX(-90deg) translateZ(25px); }
.face.l { transform: rotateY(-90deg) translateZ(25px); }
.face.r { transform: rotateY(90deg) translateZ(25px); }
.face.f { transform: rotateY(0deg) translateZ(25px); }
.face.b { transform: rotateY(180deg) translateZ(25px); }

/* Premium vibrant radial-gradient sticker colors */
.color-yellow::after {
  background: radial-gradient(circle at 35% 35%, #fffb9b 0%, #ffcc00 70%);
  border: 1px solid #d4a300;
}
.color-white::after {
  background: radial-gradient(circle at 35% 35%, #ffffff 0%, #e6e6e6 70%);
  border: 1px solid #cccccc;
}
.color-orange::after {
  background: radial-gradient(circle at 35% 35%, #ffac66 0%, #ff6b00 70%);
  border: 1px solid #cc5200;
}
.color-red::after {
  background: radial-gradient(circle at 35% 35%, #ff6680 0%, #cc0022 70%);
  border: 1px solid #990011;
}
.color-green::after {
  background: radial-gradient(circle at 35% 35%, #73ff8d 0%, #00b33c 70%);
  border: 1px solid #00802b;
}
.color-blue::after {
  background: radial-gradient(circle at 35% 35%, #66a3ff 0%, #0055ff 70%);
  border: 1px solid #0040cc;
}
.color-black::after {
  background: none;
  border: none;
  box-shadow: none;
}

/* Control Buttons & HUD */
.cube-controls {
  width: 100%;
  margin-top: 15px;
}

.control-row {
  display: flex;
  justify-content: space-between;
  gap: 10px;
  margin-bottom: 12px;
}

.manual-moves {
  flex-direction: column;
  gap: 6px;
}

.move-label {
  font-size: 0.85rem;
  color: #a1a1aa;
  font-weight: 600;
  text-transform: uppercase;
}

.move-buttons {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 6px;
}

.btn {
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #fff;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.btn:hover:not(:disabled) {
  background: rgba(255, 255, 255, 0.15);
  transform: translateY(-1px);
}

.btn:active:not(:disabled) {
  transform: translateY(0);
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  border: none;
  flex: 1;
}

.btn-primary:hover:not(:disabled) {
  background: linear-gradient(135deg, #818cf8, #6366f1);
  box-shadow: 0 0 12px rgba(99, 102, 241, 0.4);
}

.btn-secondary {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  flex: 1;
}

.btn-success {
  background: linear-gradient(135deg, #10b981, #059669);
  border: none;
  width: 100%;
}

.btn-success:hover:not(:disabled) {
  background: linear-gradient(135deg, #34d399, #10b981);
  box-shadow: 0 0 12px rgba(16, 185, 129, 0.4);
}

.btn-danger {
  background: linear-gradient(135deg, #ef4444, #dc2626);
  border: none;
  width: 100%;
}

.btn-danger:hover:not(:disabled) {
  background: linear-gradient(135deg, #f87171, #ef4444);
  box-shadow: 0 0 12px rgba(239, 68, 68, 0.4);
}

.move-btn {
  font-family: monospace;
  font-weight: 700;
  padding: 6px 0;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.08);
}

.move-btn:hover {
  background: rgba(255, 255, 255, 0.12);
  border-color: rgba(255, 255, 255, 0.2);
}

.rotation-tip {
  text-align: center;
  background: rgba(0, 0, 0, 0.2);
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.03);
}

.rotation-tip small {
  color: #a1a1aa;
}

/* Right Panel: Step Tabs & Info */
.cube-right-panel {
  display: flex;
  flex-direction: column;
  background: rgba(255, 255, 255, 0.02);
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.04);
  overflow: hidden;
  max-height: 600px;
}

.tutorial-tabs {
  display: flex;
  overflow-x: auto;
  background: rgba(0, 0, 0, 0.3);
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
  scrollbar-width: thin;
}

.tutorial-tabs::-webkit-scrollbar {
  height: 4px;
}

.tutorial-tabs::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.15);
  border-radius: 2px;
}

.tab-btn {
  background: none;
  border: none;
  color: #9ca3af;
  padding: 12px 18px;
  font-weight: 600;
  font-size: 0.9rem;
  cursor: pointer;
  white-space: nowrap;
  transition: all 0.2s ease;
  position: relative;
}

.tab-btn:hover {
  color: #fff;
  background: rgba(255, 255, 255, 0.03);
}

.tab-btn.active {
  color: #a78bfa;
  background: rgba(255, 255, 255, 0.05);
}

.tab-btn.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: #8b5cf6;
  border-radius: 3px 3px 0 0;
}

.tab-content {
  padding: 20px;
  overflow-y: auto;
  flex: 1;
}

.tab-content h3 {
  margin-top: 0;
  margin-bottom: 12px;
  font-weight: 700;
  color: #fff;
  font-size: 1.3rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  padding-bottom: 8px;
}

.tab-content h4 {
  margin-top: 15px;
  margin-bottom: 10px;
  color: #ddd;
}

.tab-content p, .tab-content li {
  line-height: 1.6;
  color: #d1d5db;
  font-size: 0.95rem;
}

.tab-content ul, .tab-content ol {
  padding-left: 20px;
  margin-bottom: 15px;
}

.tab-content li {
  margin-bottom: 8px;
}

.tab-content.hidden {
  display: none !important;
}

.step-guide {
  background: rgba(255, 255, 255, 0.03);
  border-left: 4px solid #8b5cf6;
  padding: 12px 16px;
  border-radius: 0 12px 12px 0;
  margin: 15px 0;
}

.formula-box {
  background: rgba(0, 0, 0, 0.3);
  border: 1px dashed rgba(255, 255, 255, 0.2);
  border-radius: 8px;
  padding: 10px;
  margin: 10px 0;
  font-family: monospace;
  font-size: 1.15rem;
  font-weight: 700;
  text-align: center;
  color: #e9d5ff;
}

.formula {
  background: rgba(139, 92, 246, 0.2);
  padding: 2px 6px;
  border-radius: 4px;
  color: #c084fc;
}

.notation-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
  background: rgba(0, 0, 0, 0.2);
  padding: 12px;
  border-radius: 10px;
  margin: 10px 0;
}

.notation-grid div {
  font-size: 0.9rem;
}

.formula-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin: 15px 0;
}

@media (max-width: 600px) {
  .formula-grid {
    grid-template-columns: 1fr;
  }
}

.formula-col {
  background: rgba(255, 255, 255, 0.02);
  padding: 12px;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.05);
}

.algo-player {
  margin-top: 15px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.hud-info {
  background: rgba(99, 102, 241, 0.1);
  border: 1px solid rgba(99, 102, 241, 0.2);
  color: #c7d2fe;
  padding: 8px 12px;
  border-radius: 8px;
  text-align: center;
  font-size: 0.9rem;
  margin-bottom: 12px;
  font-weight: 600;
  min-height: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Rotation wrapper dynamically created in JS */
.rotation-wrapper {
  position: absolute;
  width: 156px;
  height: 156px;
  left: 0;
  top: 0;
  transform-style: preserve-3d;
}
</style>

<div class="rubik-app-wrapper">
  <div class="rubik-app-title">Trải nghiệm Giải Rubik 3D</div>
  
  <div class="cube-workspace">
    <!-- Left Panel: 3D view and basic controls -->
    <div class="cube-left-panel">
      <div class="hud-info" id="hud-status">Kéo thả chuột để xoay góc nhìn. Sẵn sàng!</div>
      
      <div class="scene">
        <div class="cube" id="rubik-cube">
          <!-- Dynamically filled with 27 cubies by JS -->
        </div>
      </div>
      
      <div class="cube-controls">
        <div class="control-row">
          <button class="btn btn-primary" id="btn-scramble">Xáo trộn (Scramble)</button>
          <button class="btn btn-secondary" id="btn-reset">Đặt lại (Reset)</button>
        </div>
        <div class="control-row manual-moves">
          <span class="move-label">Xoay mặt (Thuận):</span>
          <div class="move-buttons">
            <button class="btn move-btn" data-move="U">U</button>
            <button class="btn move-btn" data-move="D">D</button>
            <button class="btn move-btn" data-move="L">L</button>
            <button class="btn move-btn" data-move="R">R</button>
            <button class="btn move-btn" data-move="F">F</button>
            <button class="btn move-btn" data-move="B">B</button>
          </div>
        </div>
        <div class="control-row manual-moves">
          <span class="move-label">Xoay mặt (Ngược):</span>
          <div class="move-buttons">
            <button class="btn move-btn" data-move="U'">U'</button>
            <button class="btn move-btn" data-move="D'">D'</button>
            <button class="btn move-btn" data-move="L'">L'</button>
            <button class="btn move-btn" data-move="R'">R'</button>
            <button class="btn move-btn" data-move="F'">F'</button>
            <button class="btn move-btn" data-move="B'">B'</button>
          </div>
        </div>
        <div class="control-row manual-moves">
          <span class="move-label">Xoay lớp giữa (Slice):</span>
          <div class="move-buttons" style="grid-template-columns: repeat(4, 1fr);">
            <button class="btn move-btn" data-move="M">M</button>
            <button class="btn move-btn" data-move="E">E</button>
            <button class="btn move-btn" data-move="M'">M'</button>
            <button class="btn move-btn" data-move="E'">E'</button>
          </div>
        </div>
        <div class="rotation-tip">
          <small>💡 Mẹo: Nhấn kéo chuột hoặc vuốt trên khối Rubik để xoay góc nhìn 3D!</small>
        </div>
      </div>
    </div>
    
    <!-- Right Panel: Step-by-step guides with playback buttons -->
    <div class="cube-right-panel">
      <div class="tutorial-tabs" id="tabs">
        <button class="tab-btn active" data-step="intro">Tổng quan</button>
        <button class="tab-btn" data-step="step1">1. Nhị vàng cánh trắng</button>
        <button class="tab-btn" data-step="step2">2. Xong tầng 1</button>
        <button class="tab-btn" data-step="step3">3. Xong tầng 2</button>
        <button class="tab-btn" data-step="step4">4. Chữ thập vàng</button>
        <button class="tab-btn" data-step="step5">5. Đúng tâm vàng</button>
        <button class="tab-btn" data-step="step6">6. Góc vàng</button>
        <button class="tab-btn" data-step="step7">7. Lật góc xong</button>
        <button class="tab-btn" data-step="cfop">⭐ Nâng cao (CFOP)</button>
        <button class="tab-btn" data-step="patterns">🎨 Hoa văn đẹp</button>
      </div>
      
      <!-- Tab Intro -->
      <div class="tab-content" id="tab-intro">
        <h3>Tìm hiểu cấu tạo Rubik 3x3x3</h3>
        <p>Để giải được Rubik, đầu tiên các em cần nhớ cấu tạo gồm 3 loại viên chính:</p>
        <ul>
          <li><strong>6 viên tâm (Center):</strong> Nằm ở chính giữa mỗi mặt, có đúng 1 màu và cố định, không thể dịch chuyển. Tâm màu nào đại diện cho màu của mặt đó.</li>
          <li><strong>12 viên cạnh (Edge):</strong> Có 2 màu, nằm ở giữa các cạnh.</li>
          <li><strong>8 viên góc (Corner):</strong> Có 3 màu, nằm ở các góc.</li>
        </ul>
        
        <h4>Ký hiệu xoay tiêu chuẩn (Notation)</h4>
        <p>Mỗi nước đi được quy ước bằng chữ cái đầu của mặt đó bằng tiếng Anh (khi ta nhìn thẳng vào mặt đó):</p>
        <div class="notation-grid">
          <div><strong>R (Right):</strong> Mặt Phải (xoay theo kim đồng hồ)</div>
          <div><strong>L (Left):</strong> Mặt Trái</div>
          <div><strong>U (Up):</strong> Mặt Trên</div>
          <div><strong>D (Down):</strong> Mặt Dưới</div>
          <div><strong>F (Front):</strong> Mặt Trước</div>
          <div><strong>B (Back):</strong> Mặt Sau</div>
        </div>
        <p>Nếu có dấu nháy đơn đi kèm như <strong>R' (R nháy)</strong>, xoay <strong>ngược chiều kim đồng hồ</strong>.</p>
        
        <h4>Ký hiệu xoay lớp giữa (Slice Moves)</h4>
        <p>Để tạo các hoa văn nhanh hơn, ta có thể xoay các lớp nằm ở giữa các mặt chính (nhìn từ các hướng tiêu chuẩn tương ứng):</p>
        <div class="notation-grid">
          <div><strong>M (Middle):</strong> Lớp giữa nằm giữa Trái và Phải (xoay xuống dưới, cùng chiều với L)</div>
          <div><strong>E (Equator):</strong> Lớp giữa nằm giữa Trên và Dưới (xoay sang phải, cùng chiều với D)</div>
          <div><strong>M':</strong> Xoay lớp giữa lên trên (ngược chiều với L)</div>
          <div><strong>E':</strong> Xoay lớp giữa sang trái (ngược chiều với D)</div>
        </div>
        
        <h4>Giải mẫu thử nghiệm</h4>
        <p>Hãy bấm nút bên dưới để xem máy tính xáo trộn mẫu và tự động giải Rubik từ đầu đến cuối một cách trực quan:</p>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-success" id="btn-demo-solve">Xem toàn bộ giải mẫu ảo (Demo Solve)</button>
          <button class="btn btn-secondary play-algo" data-algo="R U R' U'">Chạy thử "Sexy Move" (R U R' U')</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step1" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 1 (Nhị vàng cánh trắng) ➔</button>
      </div>
      
      <!-- Tab 1 -->
      <div class="tab-content hidden" id="tab-step1">
        <h3>Bước 1: Nhị vàng cánh trắng (Daisy) & Chữ thập trắng</h3>
        <p>Mục tiêu: Tạo chữ thập trắng ở mặt dưới đáy, sao cho phần cạnh trùng màu với tâm hông.</p>
        <div class="step-guide">
          <strong>Cách làm dễ nhất của Thầy Huy:</strong>
          <ol>
            <li><strong>Tạo "Hoa cúc Daisy":</strong> Giữ mặt vàng ở trên. Tìm 4 viên cạnh có màu trắng ở khắp khối Rubik, xoay đưa chúng về bao quanh tâm vàng.</li>
            <li><strong>Hạ cánh hoa:</strong> Nhìn vào cạnh trắng ở trên cùng. Xoay tầng trên cùng (U) sao cho màu bên hông của nó trùng khớp với màu tâm.</li>
            <li>Khi đã trùng tâm hông, xoay mặt đó 180 độ (F2, R2, B2 hoặc L2 tùy mặt) để đưa cánh hoa trắng này rơi xuống mặt đáy màu trắng.</li>
            <li>Làm tương tự cho cả 4 cạnh để có một chữ thập trắng hoàn hảo dưới đáy.</li>
          </ol>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <p><strong>Minh họa xoay 1 cạnh trắng lên hoa cúc và hạ xuống đáy:</strong></p>
          <button class="btn btn-success play-algo" data-algo="R U R'">Đưa lên hoa cúc (R U R')</button>
          <button class="btn btn-success play-algo" data-algo="F2">Hạ xuống đáy trắng (F2)</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step2" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 2 (Xong tầng 1) ➔</button>
      </div>
      
      <!-- Tab 2 -->
      <div class="tab-content hidden" id="tab-step2">
        <h3>Bước 2: Giải các viên góc Trắng (Hoàn thành Tầng 1)</h3>
        <p>Mục tiêu: Đưa 4 viên góc màu trắng về đúng góc ở mặt đáy, hoàn thành mặt trắng và tầng 1 trùng màu tâm.</p>
        <div class="step-guide">
          <strong>Các bước giải:</strong>
          <ol>
            <li>Giữ mặt trắng ở dưới đáy, mặt vàng hướng lên trên.</li>
            <li>Tìm một viên góc chứa màu trắng ở tầng 3 (tầng trên cùng). Xem 2 màu còn lại của góc đó (Ví dụ: Góc Trắng - Đỏ - Xanh lá).</li>
            <li>Xoay mặt U để đưa viên góc này về nằm ngay phía trên góc giao của 2 mặt Đỏ và Xanh lá.</li>
            <li>Để viên góc bên Phải, thực hiện công thức <strong>Sexy Move</strong> từ 1 đến 5 lần cho đến khi viên góc nằm đúng hướng dưới đáy:</li>
          </ol>
          <div class="formula-box">
            Công thức: <span class="formula">R U R' U'</span>
          </div>
          <p><em>* Mẹo: Nếu viên góc nằm sẵn ở dưới đáy nhưng bị ngược màu hoặc sai góc, làm công thức trên 1 lần để đưa nó lên tầng 3 rồi giải bình thường.</em></p>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-success play-algo" data-algo="R U R' U'">Chạy Sexy Move (R U R' U')</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step3" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 3 (Xong tầng 2) ➔</button>
      </div>
      
      <!-- Tab 3 -->
      <div class="tab-content hidden" id="tab-step3">
        <h3>Bước 3: Giải các viên cạnh tầng 2 (Hoàn thành Tầng 2)</h3>
        <p>Mục tiêu: Đưa 4 viên cạnh ở tầng 3 không có màu vàng về đúng chỗ ở tầng 2.</p>
        <div class="step-guide">
          <strong>Các bước giải:</strong>
          <ol>
            <li>Tìm các viên cạnh ở tầng 3 <strong>không chứa màu vàng</strong>. Xem màu hông của nó.</li>
            <li>Xoay tầng trên (U) cho màu hông đó trùng khớp với tâm tạo thành một đường thẳng dọc chữ T.</li>
            <li>Xem mặt trên của viên cạnh cần sang bên trái hay bên phải:</li>
          </ol>
          
          <div class="formula-grid">
            <div class="formula-col">
              <strong>Trường hợp đi sang PHẢI:</strong>
              <div class="formula-box" style="font-size: 0.95rem;">U R U' R' U' F' U F</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="U R U' R' U' F' U F">Xoay sang Phải</button>
            </div>
            <div class="formula-col">
              <strong>Trường hợp đi sang TRÁI:</strong>
              <div class="formula-box" style="font-size: 0.95rem;">U' L' U L U F U' F'</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="U' L' U L U F U' F'">Xoay sang Trái</button>
            </div>
          </div>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step4" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 4 (Chữ thập vàng) ➔</button>
      </div>
      
      <!-- Tab 4 -->
      <div class="tab-content hidden" id="tab-step4">
        <h3>Bước 4: Tạo chữ thập vàng ở mặt trên</h3>
        <p>Mục tiêu: Tạo chữ thập vàng ở mặt trên cùng mà không làm hỏng 2 tầng đã giải.</p>
        <div class="step-guide">
          <p>Nhìn vào các cạnh màu vàng ở mặt trên, ta sẽ gặp 1 trong 3 trạng thái:</p>
          <ul>
            <li><strong>Chỉ có 1 viên tâm vàng:</strong> Làm công thức dưới 1 lần sẽ ra chữ L.</li>
            <li><strong>Dạng chữ L ngược:</strong> Xoay mặt U cho 2 cạnh vàng tạo góc 9h và 12h ở góc trên trái. Làm công thức dưới ra đường thẳng nằm ngang.</li>
            <li><strong>Thanh ngang màu vàng:</strong> Xoay mặt U để thanh ngang nằm ngang. Làm công thức dưới ra chữ thập vàng.</li>
          </ul>
          <div class="formula-box">
            Công thức: <span class="formula">F R U R' U' F'</span>
          </div>
          <p><em>* Mẹo của Thầy Huy: Nếu đang có chữ L ngược, bạn xoay cả 2 lớp mặt trước (kí hiệu là <strong>f</strong>) sẽ ra chữ thập vàng ngay lập tức: <span class="formula">f R U R' U' f'</span></em></p>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-success play-algo" data-algo="F R U R' U' F'">Xem công thức F (R U R' U') F'</button>
          <button class="btn btn-success play-algo" data-algo="f R U R' U' f'" style="margin-left: 5px;">Xem công thức nhanh f (R U R' U') f'</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step5" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 5 (Đúng tâm vàng) ➔</button>
      </div>
      
      <!-- Tab 5 -->
      <div class="tab-content hidden" id="tab-step5">
        <h3>Bước 5: Hoán vị cạnh vàng cho đúng tâm hông</h3>
        <p>Mục tiêu: Đưa các cạnh của chữ thập vàng trùng màu hông với các mặt bên.</p>
        <div class="step-guide">
          <ol>
            <li>Xoay tầng trên (U) để tìm xem có bao nhiêu cạnh trùng màu hông. Xoay để có <strong>đúng 2 cạnh trùng màu</strong>.</li>
            <li><strong>Nếu 2 cạnh đúng kề nhau:</strong> Cầm Rubik sao cho 1 cạnh đúng ở phía <strong>Sau (Back)</strong>, 1 cạnh đúng ở bên <strong>Phải (Right)</strong>. Thực hiện công thức dưới đây:</li>
          </ol>
          <div class="formula-box">
            Công thức: <span class="formula">R U R' U R U2 R'</span>
          </div>
          <ol start="3">
            <li>Sau khi xoay xong, xoay mặt U thêm 1 lần nữa để tất cả các cạnh khớp tâm. Nếu ban đầu 2 cạnh đúng đối diện nhau, làm công thức trên 1 lần để chuyển về kề nhau.</li>
          </ol>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-success play-algo" data-algo="R U R' U R U2 R' U">Xem công thức Sune (R U R' U R U2 R' U)</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step6" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 6 (Góc vàng) ➔</button>
      </div>
      
      <!-- Tab 6 -->
      <div class="tab-content hidden" id="tab-step6">
        <h3>Bước 6: Đưa 4 viên góc vàng về đúng vị trí</h3>
        <p>Mục tiêu: Đưa các viên góc về đúng vị trí góc của nó (không cần đúng hướng xoay màu).</p>
        <div class="step-guide">
          <ol>
            <li>Tìm xem có viên góc nào đã nằm ở <strong>đúng vị trí</strong> hay chưa (viên có chứa 3 màu của 3 tâm kề đó).</li>
            <li>Nếu có 1 viên góc đúng: Cầm khối Rubik sao cho viên góc này nằm ở góc <strong>Trên - Trước - Phải (U-F-R)</strong>. Thực hiện công thức sau từ 1 đến 2 lần:</li>
          </ol>
          <div class="formula-box">
            Công thức: <span class="formula">U R U' L' U R' U' L</span>
          </div>
          <ol start="3">
            <li>Nếu không có góc nào đúng vị trí từ đầu, làm công thức trên ở hướng bất kỳ 1 lần để tạo ra 1 góc đúng, sau đó đặt góc đúng đó ở U-F-R rồi làm lại.</li>
          </ol>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-success play-algo" data-algo="U R U' L' U R' U' L">Xem công thức định vị góc</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step7" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 7 (Lật góc hoàn thành) ➔</button>
      </div>
      
      <!-- Tab 7 -->
      <div class="tab-content hidden" id="tab-step7">
        <h3>Bước 7: Định hướng góc vàng để hoàn thành Rubik</h3>
        <p>Mục tiêu: Lật hướng các góc vàng sao cho mặt màu vàng hướng lên trên hết để hoàn thành giải Rubik.</p>
        <div class="step-guide" style="border-left-color: #ef4444; background: rgba(239, 68, 68, 0.05);">
          <p style="color: #ef4444; font-weight: 700;">⚠️ CỰC KỲ QUAN TRỌNG - ĐỌC KỸ:</p>
          <ol>
            <li>Để viên góc màu vàng chưa hoàn chỉnh ở vị trí <strong>Trước - Trên - Phải (U-F-R)</strong>.</li>
            <li>Thực hiện công thức dưới đây liên tục 2 lần hoặc 4 lần cho đến khi viên góc vàng này lật đúng màu vàng lên trên cùng:</li>
          </ol>
          <div class="formula-box" style="border-color: #ef4444;">
            Công thức: <span class="formula" style="color: #ef4444; background: rgba(239,68,68,0.15)">R' D' R D</span>
          </div>
          <ol start="3">
            <li><strong>Lưu ý chí mạng:</strong> Khi giải xong viên thứ nhất, các tầng bên dưới sẽ bị xáo trộn lung tung. <strong>TUYỆT ĐỐI KHÔNG XOAY khối Rubik.</strong> Hãy giữ nguyên khối Rubik trên tay.</li>
            <li>Chỉ xoay tầng trên cùng <strong>U (hoặc U')</strong> để đưa viên góc vàng bị lỗi tiếp theo về góc <strong>U-F-R</strong>.</li>
            <li>Tiếp tục thực hiện công thức <code>R' D' R D</code> cho đến khi nó đúng hướng vàng. Làm tương tự cho các góc lỗi còn lại. Rubik sẽ tự động hoàn thành!</li>
          </ol>
        </div>
        <div class="algo-player" style="margin-bottom: 15px;">
          <button class="btn btn-danger play-algo" data-algo="R' D' R D R' D' R D">Thử lật góc (R' D' R D) x 2</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="cfop" style="width: 100%; padding: 12px; margin-top: 15px;">⭐ Học phương pháp giải nhanh Nâng cao (CFOP) ➔</button>
      </div>
      
      <!-- Tab CFOP -->
      <div class="tab-content hidden" id="tab-cfop">
        <h3>Phương pháp giải nhanh nâng cao CFOP</h3>
        <p>CFOP (còn gọi là phương pháp Fridrich) là phương pháp giải Rubik phổ biến nhất thế giới của các tuyển thủ tốc độ. Phương pháp này gồm 4 bước chính:</p>
        <div class="notation-grid" style="grid-template-columns: repeat(4, 1fr);">
          <div style="text-align: center;"><strong>C</strong>ross<br><small>(Chữ thập đáy)</small></div>
          <div style="text-align: center;"><strong>F</strong>2L<br><small>(2 tầng cùng lúc)</small></div>
          <div style="text-align: center;"><strong>O</strong>LL<br><small>(Định hướng tầng 3)</small></div>
          <div style="text-align: center;"><strong>P</strong>LL<br><small>(Hoán vị tầng 3)</small></div>
        </div>

        <h4>1. Cross (Giải chữ thập đáy nhanh)</h4>
        <p>Thay vì tạo hoa cúc Daisy rồi hạ xuống đáy, các tuyển thủ sẽ quan sát các cạnh trắng trong 15 giây chuẩn bị và giải trực tiếp chữ thập trắng ở mặt <strong>Dưới đáy (Bottom)</strong> trong tối đa 8 nước xoay.</p>

        <h4>2. F2L (First Two Layers - Giải 2 tầng cùng lúc)</h4>
        <p>Thay vì giải góc trắng tầng 1 rồi mới giải cạnh tầng 2, F2L gộp 2 viên này lại thành một cặp (Pair) rồi chèn vào khe (Slot) thích hợp. Việc này giúp hoàn thành cả 2 tầng chỉ trong 4 cặp nước đi.</p>
        <div class="step-guide">
          <strong>Ví dụ các cặp F2L cơ bản:</strong>
          <ul>
            <li><strong>Chèn cặp đôi có sẵn (Basic Insert):</strong> 
              <span class="formula">U R U' R'</span> (Khe bên phải) hoặc <span class="formula">U' L' U L</span> (Khe bên trái)
              <br>
              <button class="btn btn-success btn-sm play-algo" data-algo="U R U' R'" style="margin-top: 4px; padding: 4px 8px; font-size: 0.8rem;">Chạy thử U R U' R'</button>
            </li>
            <li><strong>Tách và chèn góc-cạnh (Split & Insert):</strong>
              Công thức: <span class="formula">R U R' U' R U R'</span>
              <br>
              <button class="btn btn-success btn-sm play-algo" data-algo="R U R' U' R U R'" style="margin-top: 4px; padding: 4px 8px; font-size: 0.8rem;">Chạy thử tách chèn</button>
            </li>
          </ul>
        </div>

        <h4>3. OLL (Orientation of Last Layer - Định hướng tầng 3)</h4>
        <p>Lật toàn bộ mặt màu vàng của tầng 3 lên trên trong đúng 1 công thức. CFOP đầy đủ có 57 công thức OLL, nhưng người mới bắt đầu có thể học hệ <strong>2-Look OLL</strong> (chỉ gồm 7 công thức lật góc sau khi đã có chữ thập vàng):</p>
        <div class="formula-grid">
          <div class="formula-col">
            <strong>Cá vàng thuận (Sune):</strong>
            <div class="formula-box" style="font-size: 0.95rem;">R U R' U R U2 R'</div>
            <button class="btn btn-success btn-sm play-algo" data-algo="R U R' U R U2 R'">Chạy Sune</button>
          </div>
          <div class="formula-col">
            <strong>Cá vàng ngược (Anti-Sune):</strong>
            <div class="formula-box" style="font-size: 0.95rem;">R U2 R' U' R U' R'</div>
            <button class="btn btn-success btn-sm play-algo" data-algo="R U2 R' U' R U' R'">Chạy Anti-Sune</button>
          </div>
        </div>

        <h4>4. PLL (Permutation of Last Layer - Hoán vị tầng 3)</h4>
        <p>Hoán đổi vị trí các góc và cạnh của tầng 3 để hoàn thành khối Rubik trong đúng 1 công thức. Dưới đây là các công thức PLL huyền thoại phổ biến nhất:</p>
        
        <div class="step-guide">
          <strong>Các thuật toán PLL quan trọng nhất:</strong>
          <ul>
            <li style="margin-bottom: 12px;">
              <strong>Hoán vị chữ T (T-Perm - Đổi 2 góc bên & 2 cạnh bên):</strong>
              <div class="formula-box" style="font-size: 1rem; margin: 6px 0;">R U R' U' R' F R2 U' R' U' R U R' F'</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="R U R' U' R' F R2 U' R' U' R U R' F'">Chạy minh họa T-Perm</button>
            </li>
            <li style="margin-bottom: 12px;">
              <strong>Hoán vị chữ Y (Y-Perm - Đổi 2 góc chéo & 2 cạnh chéo):</strong>
              <div class="formula-box" style="font-size: 1rem; margin: 6px 0;">F R U' R' U' R U R' F' R U R' U' R' F R F'</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="F R U' R' U' R U R' F' R U R' U' R' F R F'">Chạy minh họa Y-Perm</button>
            </li>
            <li style="margin-bottom: 12px;">
              <strong>Hoán vị chữ U bản A (U-Perm A - Đổi 3 cạnh ngược chiều):</strong>
              <div class="formula-box" style="font-size: 1rem; margin: 6px 0;">R2 U R U R' U' R' U' R' U R'</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="R2 U R U R' U' R' U' R' U R'">Chạy minh họa U-Perm A</button>
            </li>
            <li style="margin-bottom: 12px;">
              <strong>Hoán vị chữ U bản B (U-Perm B - Đổi 3 cạnh thuận chiều):</strong>
              <div class="formula-box" style="font-size: 1rem; margin: 6px 0;">R U' R U R U R U' R' U' R2</div>
              <button class="btn btn-success btn-sm play-algo" data-algo="R U' R U R U R U' R' U' R2">Chạy minh họa U-Perm B</button>
            </li>
          </ul>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="patterns" style="width: 100%; padding: 12px; margin-top: 15px;">🎨 Khám phá các hoa văn Rubik tuyệt đẹp ➔</button>
      </div>

      <!-- Tab Patterns -->
      <div class="tab-content hidden" id="tab-patterns">
        <h3>Tạo các hoa văn Rubik 3x3x3 tuyệt đẹp</h3>
        <p>Từ một khối Rubik đã giải hoàn chỉnh, các em có thể xoay theo các công thức đặc biệt dưới đây để tạo ra những mẫu hoa văn cực kỳ bắt mắt. Hãy bấm nút để khối Rubik 3D tự động xoay biểu diễn nhé!</p>
        
        <div class="step-guide">
          <strong>1. Hoa văn bàn cờ (Checkerboard)</strong>
          <p>Mẫu hoa văn đan xen màu sắc cổ điển và cực kỳ nổi tiếng. Tất cả 6 mặt sẽ biến thành hình bàn cờ vua.</p>
          <div class="formula-box">Công thức: <span class="formula">R2 L2 U2 D2 F2 B2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="R2 L2 U2 D2 F2 B2">Tạo hoa văn Bàn cờ</button>
        </div>

        <div class="step-guide">
          <strong>2. Hoa văn mắt hoa cúc / Nhụy hoa (Donut / Dot / Flower)</strong>
          <p>Tạo một chấm tròn màu khác biệt ở chính giữa 6 mặt, trông như những bông hoa hoặc chiếc bánh donut ngọt ngào.</p>
          <div class="formula-box" style="font-size: 1rem; line-height: 1.6;">
            Công thức cơ bản: <span class="formula">U D' R L' F B' U' D</span><br>
            Công thức xoay nhanh (Slice): <span class="formula">M E M' E'</span>
          </div>
          <button class="btn btn-success play-algo-pattern" data-algo="M E M' E'">Tạo hoa văn Nhụy hoa</button>
        </div>

        <div class="step-guide">
          <strong>3. Khối trong khối (Cube in a Cube)</strong>
          <p>Tạo ra một khối Rubik 2x2x2 thu nhỏ nằm lồng góc bên trong khối Rubik 3x3x3 lớn, tạo chiều sâu 3D cực đỉnh.</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">L B L U' F U L2 B2 U' B' R D' R' B2 U</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="L B L U' F U L2 B2 U' B' R D' R' B2 U">Tạo hoa văn Khối trong khối</button>
        </div>

        <div class="step-guide">
          <strong>4. Khối trong khối trong khối (Cube in a Cube in a Cube)</strong>
          <p>Tạo ra hai lớp khối Rubik thu nhỏ lồng nhau ở góc (một khối 2x2x2 và một khối 1x1x1 bên trong).</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">U' B' U' L' F2 R' F L U R2 U R' B U' L U F L'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="U' B' U' L' F2 R' F L U R2 U R' B U' L U F L'">Tạo hoa văn Khối trong khối trong khối</button>
        </div>

        <div class="step-guide">
          <strong>5. Hoa văn Con Rắn (Snake)</strong>
          <p>Tạo ra các đường sọc màu sắc uốn lượn xung quanh khối Rubik giống như một chú rắn đang bò quanh chiếc hộp.</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">L U B' U' R L' B R' F B' D R D' F'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="L U B' U' R L' B R' F B' D R D' F'">Tạo hoa văn Con rắn</button>
        </div>

        <div class="step-guide">
          <strong>6. Hoa văn Chân gà (Chicken Feet)</strong>
          <p>Tạo hiệu ứng trao đổi tâm và các góc/cạnh đặc biệt, tạo hình ảnh giống như các dấu chân gà in trên bề mặt các mặt Rubik.</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">F L' D' B' L F U F' D' F L2 B' R' U L2 D' F</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F L' D' B' L F U F' D' F L2 B' R' U L2 D' F">Tạo hoa văn Chân gà</button>
        </div>

        <div class="step-guide">
          <strong>7. Hoa văn Python (Python)</strong>
          <p>Tạo hiệu ứng hình khối hai chú rắn lồng ghép đối xứng, giống như biểu tượng (logo) ngôn ngữ lập trình Python huyền thoại.</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">F2 R' B' U R' L F' L F' B D' R B L2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F2 R' B' U R' L F' L F' B D' R B L2">Tạo hoa văn Python</button>
        </div>

        <div class="step-guide">
          <strong>8. Hoa văn Hộp quà (Gift Box)</strong>
          <p>Tạo hiệu ứng chiếc ruy-băng gói quà thắt nơ bao quanh toàn bộ khối Rubik giống như một hộp quà Giáng sinh dễ thương.</p>
          <div class="formula-box" style="font-size: 1rem;">Công thức: <span class="formula">U B2 R2 B2 L2 F2 R2 D' F2 L2 B F' L F2 D U' R2 F' L' R'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="U B2 R2 B2 L2 F2 R2 D' F2 L2 B F' L F2 D U' R2 F' L' R'">Tạo hoa văn Hộp quà</button>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
// Javascript Rubik's Cube 3D Engine & Logic
(function() {
  const cubeEl = document.getElementById('rubik-cube');
  const hudStatus = document.getElementById('hud-status');
  
  // Initial colors
  const defaultColors = {
    u: 'color-yellow',
    d: 'color-white',
    l: 'color-orange',
    r: 'color-red',
    f: 'color-green',
    b: 'color-blue',
    internal: 'color-black'
  };
  
  let cubies = [];
  let isAnimating = false;
  let moveQueue = [];
  let animSpeedMs = 400; // default for manual moves (400ms rotate, 100ms pause)
  let animPauseMs = 100;
  
  // Drag to rotate view variables
  let cubeRx = -25;
  let cubeRy = 45;
  let isDragging = false;
  let startX = 0;
  let startY = 0;
  
  // Generate initial state of 27 cubies
  function initCube() {
    cubies = [];
    cubeEl.innerHTML = '';
    
    for (let x = -1; x <= 1; x++) {
      for (let y = -1; y <= 1; y++) {
        for (let z = -1; z <= 1; z++) {
          const faces = {
            u: (y === -1) ? defaultColors.u : defaultColors.internal,
            d: (y === 1) ? defaultColors.d : defaultColors.internal,
            l: (x === -1) ? defaultColors.l : defaultColors.internal,
            r: (x === 1) ? defaultColors.r : defaultColors.internal,
            f: (z === 1) ? defaultColors.f : defaultColors.internal,
            b: (z === -1) ? defaultColors.b : defaultColors.internal
          };
          
          cubies.push({ x, y, z, faces, id: `cubie-${x}-${y}-${z}` });
        }
      }
    }
    renderCube();
  }
  
  // Render cube state into DOM elements
  function renderCube() {
    // Clear dynamic wrapper/content
    cubeEl.innerHTML = '';
    cubies.forEach(c => {
      const cubieDiv = document.createElement('div');
      cubieDiv.className = 'cubie';
      cubieDiv.id = c.id;
      cubieDiv.style.setProperty('--x', c.x);
      cubieDiv.style.setProperty('--y', c.y);
      cubieDiv.style.setProperty('--z', c.z);
      
      // Face divs
      const faceDirections = ['u', 'd', 'l', 'r', 'f', 'b'];
      faceDirections.forEach(dir => {
        const faceDiv = document.createElement('div');
        faceDiv.className = `face ${dir} ${c.faces[dir]}`;
        cubieDiv.appendChild(faceDiv);
      });
      
      cubeEl.appendChild(cubieDiv);
    });
  }
  
  // Execute a face rotation move (e.g. 'R', "R'")
  function makeMove(moveCode, callback) {
    if (isAnimating) {
      moveQueue.push({ moveCode, callback });
      return;
    }
    
    isAnimating = true;
    const isPrime = moveCode.includes("'");
    const baseMove = moveCode.replace("'", "");
    
    let filterFunc;
    let rotationAxis;
    let cssAngle;
    let logicalDir = isPrime ? 'CCW' : 'CW';
    
    // Explicit mapping of physical CSS angle and logical direction for each move
    switch (baseMove) {
      case 'R':
        filterFunc = c => c.x === 1;
        rotationAxis = 'X';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'L':
        filterFunc = c => c.x === -1;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'U':
        filterFunc = c => c.y === -1;
        rotationAxis = 'Y';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'D':
        filterFunc = c => c.y === 1;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'F':
        filterFunc = c => c.z === 1;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'f': // double layer F
        filterFunc = c => c.z >= 0;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'B':
        filterFunc = c => c.z === -1;
        rotationAxis = 'Z';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'M':
        filterFunc = c => c.x === 0;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'E':
        filterFunc = c => c.y === 0;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      default:
        isAnimating = false;
        if (callback) callback();
        return;
    }
    
    const rotatingCubies = cubies.filter(filterFunc);
    
    // Create temporary animation wrapper
    const wrapper = document.createElement('div');
    wrapper.className = 'rotation-wrapper';
    cubeEl.appendChild(wrapper);
    
    // Move elements to wrapper
    rotatingCubies.forEach(c => {
      const el = document.getElementById(c.id);
      if (el) wrapper.appendChild(el);
    });
    
    // Animate wrapper with dynamic speed
    wrapper.style.transition = `transform ${animSpeedMs / 1000}s cubic-bezier(0.25, 1, 0.5, 1)`;
    // Force reflow
    wrapper.offsetHeight;
    
    wrapper.style.transform = `rotate${rotationAxis}(${cssAngle}deg)`;
    
    setTimeout(() => {
      // 1. Math State updates
      rotatingCubies.forEach(c => {
        let x = c.x;
        let y = c.y;
        let z = c.z;
        let temp;
        
        if (rotationAxis === 'X') {
          if (baseMove === 'R') {
            if (logicalDir === 'CW') {
              c.y = -z; c.z = y;
              temp = c.faces.u; c.faces.u = c.faces.f; c.faces.f = c.faces.d; c.faces.d = c.faces.b; c.faces.b = temp;
            } else {
              c.y = z; c.z = -y;
              temp = c.faces.u; c.faces.u = c.faces.b; c.faces.b = c.faces.d; c.faces.d = c.faces.f; c.faces.f = temp;
            }
          } else { // L or M
            if (logicalDir === 'CW') {
              c.y = z; c.z = -y;
              temp = c.faces.u; c.faces.u = c.faces.b; c.faces.b = c.faces.d; c.faces.d = c.faces.f; c.faces.f = temp;
            } else {
              c.y = -z; c.z = y;
              temp = c.faces.u; c.faces.u = c.faces.f; c.faces.f = c.faces.d; c.faces.d = c.faces.b; c.faces.b = temp;
            }
          }
        } else if (rotationAxis === 'Y') {
          if (baseMove === 'U') {
            if (logicalDir === 'CW') {
              c.x = -z; c.z = x;
              temp = c.faces.l; c.faces.l = c.faces.f; c.faces.f = c.faces.r; c.faces.r = c.faces.b; c.faces.b = temp;
            } else {
              c.x = z; c.z = -x;
              temp = c.faces.l; c.faces.l = c.faces.b; c.faces.b = c.faces.r; c.faces.r = c.faces.f; c.faces.f = temp;
            }
          } else { // D or E
            if (logicalDir === 'CW') {
              c.x = z; c.z = -x;
              temp = c.faces.l; c.faces.l = c.faces.b; c.faces.b = c.faces.r; c.faces.r = c.faces.f; c.faces.f = temp;
            } else {
              c.x = -z; c.z = x;
              temp = c.faces.l; c.faces.l = c.faces.f; c.faces.f = c.faces.r; c.faces.r = c.faces.b; c.faces.b = temp;
            }
          }
        } else if (rotationAxis === 'Z') {
          if (baseMove === 'F' || baseMove === 'f') {
            if (logicalDir === 'CW') {
              c.x = -y; c.y = x;
              temp = c.faces.u; c.faces.u = c.faces.l; c.faces.l = c.faces.d; c.faces.d = c.faces.r; c.faces.r = temp;
            } else {
              c.x = y; c.y = -x;
              temp = c.faces.u; c.faces.u = c.faces.r; c.faces.r = c.faces.d; c.faces.d = c.faces.l; c.faces.l = temp;
            }
          } else { // B
            if (logicalDir === 'CW') {
              c.x = y; c.y = -x;
              temp = c.faces.u; c.faces.u = c.faces.r; c.faces.r = c.faces.d; c.faces.d = c.faces.l; c.faces.l = temp;
            } else {
              c.x = -y; c.y = x;
              temp = c.faces.u; c.faces.u = c.faces.l; c.faces.l = c.faces.d; c.faces.d = c.faces.r; c.faces.r = temp;
            }
          }
        }
      });
      
      // 2. Re-render static cube in final state
      renderCube();
      
      // Clean up wrapper
      if (wrapper.parentNode) {
        wrapper.parentNode.removeChild(wrapper);
      }
      
      isAnimating = false;
      if (callback) callback();
      
      // Process next queued moves
      if (moveQueue.length > 0) {
        const next = moveQueue.shift();
        makeMove(next.moveCode, next.callback);
      }
    }, animSpeedMs + animPauseMs);
  }
  
  // Play algorithms sequentially
  function playAlgorithm(algoString, onComplete) {
    // Save current speeds
    const prevSpeed = animSpeedMs;
    const prevPause = animPauseMs;
    
    // Set tutorial speed (1.5s animation + 0.5s pause = 2 seconds per move)
    animSpeedMs = 1500;
    animPauseMs = 500;
    
    // Parse moves like "R U R' U2 F2 f"
    const moves = algoString.split(/\s+/).filter(m => m.trim().length > 0);
    const parsedMoves = [];
    
    moves.forEach(m => {
      // expand double moves: R2 -> R R, U2 -> U U, F2 -> F F etc.
      if (m.endsWith("2")) {
        const base = m.slice(0, -1);
        parsedMoves.push(base);
        parsedMoves.push(base);
      } else {
        parsedMoves.push(m);
      }
    });
    
    if (parsedMoves.length === 0) {
      animSpeedMs = prevSpeed;
      animPauseMs = prevPause;
      if (onComplete) onComplete();
      return;
    }
    
    let index = 0;
    function doNext() {
      if (index < parsedMoves.length) {
        const mv = parsedMoves[index];
        hudStatus.innerText = `Đang chạy minh họa: ${parsedMoves.slice(0, index).join(' ')} -> [${mv}] -> ${parsedMoves.slice(index + 1).join(' ')}`;
        index++;
        makeMove(mv, doNext);
      } else {
        hudStatus.innerText = `Xong công thức: ${algoString}`;
        // Restore previous speeds
        animSpeedMs = prevSpeed;
        animPauseMs = prevPause;
        if (onComplete) onComplete();
      }
    }
    doNext();
  }
  
  // Scramble the cube randomly
  function scrambleCube() {
    if (isAnimating) return;
    hudStatus.innerText = "Đang xáo trộn ngẫu nhiên...";
    
    // Scramble fast (200ms animation + 50ms pause)
    animSpeedMs = 200;
    animPauseMs = 50;
    
    const moveOptions = ['U', 'D', 'L', 'R', 'F', 'B', "U'", "D'", "L'", "R'", "F'", "B'"];
    let count = 20;
    
    function scrambleStep() {
      if (count > 0) {
        const randomMove = moveOptions[Math.floor(Math.random() * moveOptions.length)];
        count--;
        makeMove(randomMove, scrambleStep);
      } else {
        hudStatus.innerText = "Đã xáo trộn xong! Nhấn kéo để xoay góc nhìn hoặc thực hiện các bước giải.";
        // Restore manual speed
        animSpeedMs = 400;
        animPauseMs = 100;
      }
    }
    scrambleStep();
  }
  
  // Init elements & listeners
  initCube();
  
  // Scramble & Reset click listeners
  document.getElementById('btn-scramble').addEventListener('click', scrambleCube);
  document.getElementById('btn-reset').addEventListener('click', () => {
    if (isAnimating) return;
    initCube();
    hudStatus.innerText = "Khối Rubik đã được khôi phục về trạng thái ban đầu.";
  });
  
  // Manual move button listeners
  document.querySelectorAll('.move-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      const mv = btn.getAttribute('data-move');
      if (mv && !isAnimating) {
        hudStatus.innerText = `Xoay nước: ${mv}`;
        makeMove(mv);
      }
    });
  });
  
  // Play algorithm buttons listener
  document.addEventListener('click', e => {
    if (e.target && e.target.classList.contains('play-algo')) {
      const algo = e.target.getAttribute('data-algo');
      if (algo) {
        playAlgorithm(algo);
      }
    }
    // Play algorithm for patterns (resets cube state first)
    if (e.target && e.target.classList.contains('play-algo-pattern')) {
      const algo = e.target.getAttribute('data-algo');
      if (algo && !isAnimating) {
        initCube();
        hudStatus.innerText = "Khôi phục khối Rubik sạch trước khi tạo hoa văn...";
        setTimeout(() => {
          // Play at moderately fast speed (500ms per move) for patterns
          const prevSpeed = animSpeedMs;
          const prevPause = animPauseMs;
          animSpeedMs = 500;
          animPauseMs = 150;
          playAlgorithm(algo, () => {
            animSpeedMs = prevSpeed;
            animPauseMs = prevPause;
          });
        }, 500);
      }
    }
  });
  
  // 3D View Drag logic
  function setCubeRotation() {
    cubeEl.style.transform = `rotateX(${cubeRx}deg) rotateY(${cubeRy}deg)`;
  }
  
  const handleStart = (clientX, clientY) => {
    isDragging = true;
    startX = clientX;
    startY = clientY;
  };
  
  const handleMove = (clientX, clientY) => {
    if (!isDragging) return;
    const dx = clientX - startX;
    const dy = clientY - startY;
    
    cubeRy += dx * 0.55;
    cubeRx -= dy * 0.55;
    
    // limit vertical rotation to avoid flip confusion
    cubeRx = Math.max(-60, Math.min(60, cubeRx));
    
    startX = clientX;
    startY = clientY;
    setCubeRotation();
  };
  
  // Mouse events
  cubeEl.addEventListener('mousedown', e => {
    // only drag on left click
    if (e.button === 0) {
      handleStart(e.clientX, e.clientY);
    }
  });
  
  window.addEventListener('mousemove', e => {
    handleMove(e.clientX, e.clientY);
  });
  
  window.addEventListener('mouseup', () => {
    isDragging = false;
  });
  
  // Touch events
  cubeEl.addEventListener('touchstart', e => {
    if (e.touches.length === 1) {
      handleStart(e.touches[0].clientX, e.touches[0].clientY);
    }
  }, { passive: true });
  
  window.addEventListener('touchmove', e => {
    if (isDragging && e.touches.length === 1) {
      handleMove(e.touches[0].clientX, e.touches[0].clientY);
    }
  }, { passive: true });
  
  window.addEventListener('touchend', () => {
    isDragging = false;
  });
  
  // Tutorial Tabs switching logic
  const tabButtons = document.querySelectorAll('.tab-btn');
  const tabContents = document.querySelectorAll('.tab-content');
  
  tabButtons.forEach(btn => {
    btn.addEventListener('click', () => {
      // remove active classes
      tabButtons.forEach(b => b.classList.remove('active'));
      tabContents.forEach(c => c.classList.add('hidden'));
      
      // set active
      btn.classList.add('active');
      const step = btn.getAttribute('data-step');
      const content = document.getElementById(`tab-${step}`);
      if (content) {
        content.classList.remove('hidden');
      }
    });
  });
  
  // Next Step navigation buttons listener
  document.addEventListener('click', e => {
    if (e.target && e.target.classList.contains('next-step-btn')) {
      const nextStep = e.target.getAttribute('data-next');
      const targetTab = document.querySelector(`.tab-btn[data-step="${nextStep}"]`);
      if (targetTab) {
        targetTab.click();
        const rightPanel = document.querySelector('.cube-right-panel');
        if (rightPanel) rightPanel.scrollTop = 0;
      }
    }
  });

  // Demo Solve button listener
  const btnDemoSolve = document.getElementById('btn-demo-solve');
  if (btnDemoSolve) {
    btnDemoSolve.addEventListener('click', () => {
      if (isAnimating) return;
      
      initCube();
      hudStatus.innerText = "Đang tạo thế xáo trộn mẫu (chạy nhanh)...";
      
      // Scramble fast
      animSpeedMs = 250;
      animPauseMs = 50;
      playAlgorithm("R U R' U' F' U F", () => {
        hudStatus.innerText = "Thế xáo trộn đã sẵn sàng! Bắt đầu giải mẫu tự động sau 1.5 giây...";
        
        setTimeout(() => {
          // Play solve slowly (2s animation + 1s pause = 3 seconds total per move)
          playAlgorithm("F' U' F U R U R'", () => {
            hudStatus.innerText = "Giải mẫu hoàn tất! Khối Rubik đã được giải quyết thành công.";
          });
        }, 1500);
      });
    });
  }
})();
</script>
