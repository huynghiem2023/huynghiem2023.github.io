# Hướng dẫn chơi Rubik 4x4x4

Ứng dụng tương tác 3D và hướng dẫn giải Rubik 4x4x4 (Rubik's Revenge) theo phương pháp rút gọn (Reduction Method) cực kỳ dễ hiểu của thầy Huy, giúp các em học sinh dễ dàng làm quen và tự giải được khối Rubik 4x4x4 tại nhà.

```{raw} html
<style>
/* Modern premium glassmorphism styling for Rubik 4x4 tutorial app */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');

.rubik-app-wrapper {
  font-family: 'Outfit', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  color: #f3f4f6;
  background: radial-gradient(circle at top left, #1b2030, #0c0e17);
  padding: 24px;
  border-radius: 20px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.45);
  border: 1px solid rgba(255, 255, 255, 0.08);
  margin: 20px 0;
  overflow: hidden;
}

.rubik-app-title {
  text-align: center;
  font-weight: 700;
  font-size: 2rem;
  background: linear-gradient(135deg, #a78bfa, #60a5fa);
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

.cube {
  position: relative;
  width: 120px;
  height: 120px;
  transform-style: preserve-3d;
  transform: rotateX(-25deg) rotateY(45deg);
  cursor: grab;
  transition: transform 0.1s ease-out;
}

.cube:active {
  cursor: grabbing;
}

/* 4x4 Cubie pieces */
.cubie {
  position: absolute;
  width: 36px;
  height: 36px;
  left: 42px;
  top: 42px;
  transform-style: preserve-3d;
  transform: translate3d(calc(var(--x) * 38px), calc(var(--y) * 38px), calc(var(--z) * 38px));
}

.cubie.animating {
  transition: transform 0.4s ease-in-out;
}

.face {
  position: absolute;
  width: 35px;
  height: 35px;
  background-color: #121214;
  border: 1px solid #08080a;
  border-radius: 4px;
  box-sizing: border-box;
  display: flex;
  justify-content: center;
  align-items: center;
  backface-visibility: visible;
}

.face::after {
  content: '';
  width: 28px;
  height: 28px;
  border-radius: 3px;
  box-shadow: inset 0 1px 2px rgba(255, 255, 255, 0.3), inset 0 -1px 2px rgba(0, 0, 0, 0.3);
  transition: background 0.3s ease;
  box-sizing: border-box;
}

/* 3D face transforms */
.face.u { transform: rotateX(90deg) translateZ(18px); }
.face.d { transform: rotateX(-90deg) translateZ(18px); }
.face.l { transform: rotateY(-90deg) translateZ(18px); }
.face.r { transform: rotateY(90deg) translateZ(18px); }
.face.f { transform: rotateY(0deg) translateZ(18px); }
.face.b { transform: rotateY(180deg) translateZ(18px); }

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

.cube-controls {
  width: 100%;
  margin-top: 15px;
}

.control-row {
  display: flex;
  justify-content: space-between;
  gap: 8px;
  margin-bottom: 12px;
}

.manual-moves {
  flex-direction: column;
  gap: 6px;
}

.move-label {
  font-size: 0.8rem;
  color: #a1a1aa;
  font-weight: 600;
  text-transform: uppercase;
}

.move-buttons {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 4px;
}

.btn {
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #fff;
  padding: 8px 10px;
  border-radius: 8px;
  font-size: 0.85rem;
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

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  background: linear-gradient(135deg, #4f46e5, #3b82f6);
  border: none;
  flex: 1;
}

.btn-primary:hover:not(:disabled) {
  background: linear-gradient(135deg, #6366f1, #60a5fa);
  box-shadow: 0 0 12px rgba(59, 130, 246, 0.4);
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

.move-btn {
  font-family: monospace;
  font-weight: 700;
  padding: 6px 0;
  font-size: 0.8rem;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.08);
}

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
  color: #60a5fa;
  background: rgba(255, 255, 255, 0.05);
}

.tab-btn.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: #3b82f6;
  border-radius: 3px 3px 0 0;
}

.tab-content {
  padding: 20px;
  overflow-y: auto;
  flex: 1;
}

.tab-header-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  padding-bottom: 8px;
  margin-bottom: 12px;
}

.tab-header-row h3 {
  margin: 0 !important;
  font-weight: 700;
  color: #fff;
  font-size: 1.25rem;
}

.btn-read-aloud {
  background: rgba(96, 165, 250, 0.15);
  border: 1px solid rgba(96, 165, 250, 0.35);
  color: #93c5fd;
  font-size: 0.85rem;
  padding: 6px 12px;
  border-radius: 8px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 6px;
  transition: all 0.2s ease;
  font-family: inherit;
  font-weight: 600;
}

.btn-read-aloud:hover {
  background: rgba(96, 165, 250, 0.3);
  box-shadow: 0 0 10px rgba(96, 165, 250, 0.15);
}

.btn-read-aloud.active {
  background: linear-gradient(135deg, #ef4444, #dc2626);
  border: none;
  color: white;
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
  border-left: 4px solid #3b82f6;
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
  font-size: 1.1rem;
  font-weight: 700;
  text-align: center;
  color: #93c5fd;
  word-break: break-all;
}

.formula {
  background: rgba(59, 130, 246, 0.2);
  padding: 2px 6px;
  border-radius: 4px;
  color: #60a5fa;
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

.algo-player {
  margin-top: 15px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.hud-info {
  background: rgba(96, 165, 250, 0.1);
  border: 1px solid rgba(96, 165, 250, 0.2);
  color: #93c5fd;
  padding: 8px 12px;
  border-radius: 8px;
  text-align: center;
  font-size: 0.85rem;
  margin-bottom: 12px;
  font-weight: 600;
  min-height: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.rotation-wrapper {
  position: absolute;
  width: 120px;
  height: 120px;
  left: 0;
  top: 0;
  transform-style: preserve-3d;
}
</style>

<div class="rubik-app-wrapper">
  <div class="rubik-app-title">Trải nghiệm Giải Rubik 4x4x4 3D</div>
  
  <div class="cube-workspace">
    <!-- Left Panel: 3D view and controls -->
    <div class="cube-left-panel">
      <div class="hud-info" id="hud-status">Kéo thả chuột để xoay khối 4x4. Sẵn sàng!</div>
      
      <div class="scene">
        <div class="cube" id="rubik-cube">
          <!-- Populated by JS -->
        </div>
      </div>
      
      <div class="cube-controls">
        <div class="control-row">
          <button class="btn btn-primary" id="btn-scramble">Xáo trộn (Scramble)</button>
          <button class="btn btn-secondary" id="btn-reset">Đặt lại (Reset)</button>
        </div>
        
        <div class="control-row manual-moves">
          <span class="move-label">Xoay mặt ngoài (Thuận):</span>
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
          <span class="move-label">Xoay mặt ngoài (Ngược):</span>
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
          <span class="move-label">Xoay 2 lớp ngoài (Double Layer):</span>
          <div class="move-buttons">
            <button class="btn move-btn" data-move="u">Uw</button>
            <button class="btn move-btn" data-move="d">Dw</button>
            <button class="btn move-btn" data-move="l">Lw</button>
            <button class="btn move-btn" data-move="r">Rw</button>
            <button class="btn move-btn" data-move="f">Fw</button>
            <button class="btn move-btn" data-move="b">Bw</button>
          </div>
        </div>

        <div class="control-row manual-moves">
          <span class="move-label">Xoay 2 lớp (Ngược):</span>
          <div class="move-buttons">
            <button class="btn move-btn" data-move="u'">Uw'</button>
            <button class="btn move-btn" data-move="d'">Dw'</button>
            <button class="btn move-btn" data-move="l'">Lw'</button>
            <button class="btn move-btn" data-move="r'">Rw'</button>
            <button class="btn move-btn" data-move="f'">Fw'</button>
            <button class="btn move-btn" data-move="b'">Bw'</button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Right Panel: Step Tabs -->
    <div class="cube-right-panel">
      <div class="tutorial-tabs" id="tabs">
        <button class="tab-btn active" data-step="intro">Tổng quan</button>
        <button class="tab-btn" data-step="step1">1. Giải 6 Tâm</button>
        <button class="tab-btn" data-step="step2">2. Ghép Cặp Cạnh</button>
        <button class="tab-btn" data-step="step3">3. Giải như 3x3</button>
        <button class="tab-btn" data-step="parity1">4. OLL Parity</button>
        <button class="tab-btn" data-step="parity2">5. PLL Parity</button>
        <button class="tab-btn" data-step="yau">⭐ Nâng cao (Yau / 3-2-3)</button>
        <button class="tab-btn" data-step="patterns">🎨 Hoa văn 4x4</button>
      </div>
      
      <!-- Tab Intro -->
      <div class="tab-content" id="tab-intro">
        <div class="tab-header-row">
          <h3>Tổng quan cấu tạo Rubik 4x4x4</h3>
          <button class="btn-read-aloud" data-target="tab-intro">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Khối Rubik 4x4x4 (còn gọi là Rubik's Revenge) có cấu tạo gồm 64 viên nhỏ:</p>
        <ul>
          <li><strong>24 viên tâm (Center):</strong> Mỗi mặt có 4 viên tâm. Khác với 3x3, các viên tâm của 4x4 <strong>không cố định</strong>, có thể di chuyển và tráo đổi vị trí cho nhau.</li>
          <li><strong>24 viên cạnh (Edge):</strong> Gồm 12 cặp cạnh. Mỗi cạnh của khối được cấu tạo từ 2 viên riêng biệt.</li>
          <li><strong>8 viên góc (Corner):</strong> Có 3 màu sắc, nằm ở các góc của khối (hoàn toàn giống Rubik 3x3).</li>
        </ul>
        
        <h4>Thứ tự màu tâm tiêu chuẩn (Rất quan trọng)</h4>
        <p>Vì tâm 4x4 có thể xoay sai lệch, bạn phải ghi nhớ quy tắc phối màu chuẩn khi giải. Cầm khối Rubik sao cho:</p>
        <div class="step-guide">
          Giữ mặt <strong>Trắng (White)</strong> ở dưới đáy, mặt <strong>Vàng (Yellow)</strong> ở trên đỉnh. Thứ tự 4 mặt bên theo chiều kim đồng hồ quanh khối sẽ là:<br>
          <strong>Đỏ (Front) ➔ Xanh lá (Right) ➔ Cam (Back) ➔ Xanh dương (Left)</strong>
        </div>

        <h4>Ký hiệu xoay 4x4 tiêu chuẩn</h4>
        <p>Quy ước tương tự 3x3, nhưng bổ sung ký hiệu xoay cả hai lớp ngoài cùng:</p>
        <div class="notation-grid">
          <div><strong>R / L / U / D / F / B:</strong> Xoay 1 lớp ngoài cùng tương ứng</div>
          <div><strong>Rw / Lw / Uw / Dw / Fw / Bw:</strong> Xoay đồng thời cả 2 lớp ngoài cùng sát nhau (trên hình 3D ký hiệu là chữ thường: r, l, u, d, f, b)</div>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step1" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 1 (Giải 6 Tâm) ➔</button>
      </div>

      <!-- Tab 1 -->
      <div class="tab-content hidden" id="tab-step1">
        <div class="tab-header-row">
          <h3>Bước 1: Giải 6 tâm khối Rubik 4x4x4</h3>
          <button class="btn-read-aloud" data-target="tab-step1">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Mục tiêu: Đưa các viên tâm đơn lẻ về đúng vị trí để tạo thành 6 khối tâm 2x2 đồng màu trên 6 mặt.</p>
        
        <div class="step-guide">
          <strong>Trình tự giải khuyến nghị của Thầy Huy:</strong>
          <ol>
            <li><strong>Tạo tâm Trắng:</strong> Tìm và ghép 4 viên tâm trắng lại thành một khối 2x2. Đặt tâm trắng này ở mặt đáy (Bottom).</li>
            <li><strong>Tạo tâm Vàng đối diện:</strong> Xoay đưa các viên tâm vàng lên mặt trên (Up) mà không làm hỏng tâm trắng ở đáy.</li>
            <li><strong>Giải 4 tâm bên hông:</strong> Giải lần lượt các tâm bên theo đúng thứ tự: Đỏ, Xanh lá, Cam, và Xanh dương. Hãy luôn kiểm tra sự trùng khớp vị trí với góc để tránh bị ngược hệ màu.</li>
          </ol>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step2" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 2 (Ghép Cặp Cạnh) ➔</button>
      </div>

      <!-- Tab 2 -->
      <div class="tab-content hidden" id="tab-step2">
        <div class="tab-header-row">
          <h3>Bước 2: Ghép 12 cặp viên cạnh (Edge Pairing)</h3>
          <button class="btn-read-aloud" data-target="tab-step2">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Mục tiêu: Tìm các viên cạnh cùng màu và ghép chúng thành một cặp cạnh thống nhất, chuẩn bị chuyển khối về dạng 3x3.</p>
        
        <div class="step-guide">
          <strong>Cách thực hiện cơ bản:</strong>
          <ol>
            <li>Tìm 2 viên cạnh có cùng màu sắc (ví dụ: Đỏ - Xanh lá). Xoay các lớp ngoài để đưa 2 viên này về nằm song song ở mặt trước (nhưng lệch tầng nhau).</li>
            <li>Thực hiện thuật toán ghép cạnh cơ bản để tráo đổi và ghép cặp:</li>
          </ol>
          <div class="formula-box">
            Công thức ghép cạnh: <span class="formula">d R F' U R' F d'</span>
          </div>
          <p><em>* Giải thích: Xoay lớp dưới đôi (d) sang phải để ghép cặp cạnh, đưa cặp cạnh lên tầng trên và thay thế bằng một cạnh chưa giải để bảo toàn, sau đó trả lớp dưới đôi (d') về lại vị trí cũ.</em></p>
        </div>
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="d R F' U R' F d'">Chạy thử thuật toán ghép cạnh (d R F' U R' F d')</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step3" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 3 (Giải như 3x3) ➔</button>
      </div>

      <!-- Tab 3 -->
      <div class="tab-content hidden" id="tab-step3">
        <div class="tab-header-row">
          <h3>Bước 3: Giải Rubik tương đương khối 3x3x3</h3>
          <button class="btn-read-aloud" data-target="tab-step3">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Sau khi hoàn thành 6 tâm và ghép đôi toàn bộ 12 cạnh, khối Rubik 4x4x4 bây giờ hoạt động hoàn toàn giống như một khối Rubik 3x3x3 tiêu chuẩn:</p>
        <ul>
          <li>Mỗi khối tâm 2x2 tương đương với 1 viên tâm của 3x3.</li>
          <li>Mỗi cặp cạnh đã ghép tương đương với 1 viên cạnh của 3x3.</li>
          <li>8 viên góc giữ nguyên vai trò.</li>
        </ul>
        <div class="step-guide">
          Áp dụng phương pháp giải Rubik 3x3 cơ bản để hoàn thành tầng 1, tầng 2 và định hướng dấu thập tầng 3. Nếu gặp các trường hợp không thể giải được bình thường, đó là các lỗi **Parity** đặc thù của 4x4. Hãy chuyển qua Tab tiếp theo để xem công thức khắc phục.
        </div>
        <button class="btn btn-primary next-step-btn" data-next="parity1" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 4 (OLL Parity) ➔</button>
      </div>

      <!-- Tab Parity 1 -->
      <div class="tab-content hidden" id="tab-parity1">
        <div class="tab-header-row">
          <h3>Bước 4: Khắc phục lỗi lật cạnh (OLL Parity)</h3>
          <button class="btn-read-aloud" data-target="tab-parity1">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Lỗi **OLL Parity** xảy ra khi bạn có đúng một cặp cạnh ở tầng 3 bị lật ngược hướng màu. Ở khối 3x3x3 thông thường, số viên cạnh bị lật luôn là số chẵn, nên đây là trường hợp đặc biệt chỉ có từ khối 4x4 trở lên.</p>
        
        <div class="step-guide">
          <strong>Cách xử lý:</strong> Hướng cặp cạnh bị lỗi về phía mặt Trước (Front) - Trên (Up) và thực hiện thuật toán lật cạnh kinh điển:
          <div class="formula-box" style="font-size: 0.95rem; line-height: 1.6;">
            Công thức OLL Parity:<br>
            <span class="formula">r2 B2 U2 l U2 r' U2 r U2 F2 r F2 l' B2 r2</span>
          </div>
          <p><em>* Lưu ý: Ký hiệu viết thường r, l nghĩa là xoay cả 2 lớp bên phải, bên trái.</em></p>
        </div>
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="r r B B U U l U U r' U U r U U F F r F F l' B B r r">Chạy thuật toán lật cạnh OLL Parity</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="parity2" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 5 (PLL Parity) ➔</button>
      </div>

      <!-- Tab Parity 2 -->
      <div class="tab-content hidden" id="tab-parity2">
        <div class="tab-header-row">
          <h3>Bước 5: Khắc phục lỗi góc/cạnh kề (PLL Parity)</h3>
          <button class="btn-read-aloud" data-target="tab-parity2">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Lỗi **PLL Parity** xuất hiện ở bước cuối cùng khi giải tầng 3. Bạn đã giải xong hết các mặt nhưng còn lại đúng hai cặp cạnh hoặc hai viên góc bị hoán đổi vị trí chéo/kề nhau.</p>
        
        <div class="step-guide">
          <strong>Cách xử lý:</strong> Đặt hai cặp cạnh bị lỗi đối diện nhau (hoặc đặt ở mặt Trước và mặt Sau) rồi thực hiện công thức tráo đổi cực nhanh dưới đây:
          <div class="formula-box">
            Công thức PLL Parity: <span class="formula">r2 U2 r2 u2 r2 u2</span>
          </div>
          <p><em>* Thuật toán này sử dụng các lát cắt đôi trong để hoán vị các khối cạnh mà không làm ảnh hưởng đến các tầng đã hoàn thành trước đó.</em></p>
        </div>
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="r r U U r r u u r r u u">Chạy thuật toán sửa góc/cạnh PLL Parity</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="yau" style="width: 100%; padding: 12px; margin-top: 15px;">⭐ Học phương pháp giải nhanh Nâng cao (Yau) ➔</button>
      </div>

      <!-- Tab Yau Method & 3-2-3 Edge Pairing -->
      <div class="tab-content hidden" id="tab-yau">
        <div class="tab-header-row">
          <h3>Phương pháp nâng cao Yau & Ghép cạnh 3-2-3</h3>
          <button class="btn-read-aloud" data-target="tab-yau">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Để tăng tốc độ giải Rubik 4x4x4 xuống dưới 1 phút, các speedcuber hàng đầu sẽ chuyển từ phương pháp rút gọn (Reduction) cơ bản sang phương pháp <strong>Yau Method</strong> kết hợp kỹ thuật <strong>ghép cạnh 3-2-3</strong>.</p>
        
        <h4>Các bước thực hiện phương pháp Yau</h4>
        <div class="step-guide">
          <ol>
            <li><strong>Giải 2 tâm đối diện:</strong> Thường giải quyết tâm Trắng ở mặt đáy (Bottom) và tâm Vàng ở mặt trên (Up) trước.</li>
            <li><strong>Giải 3 cạnh chữ thập đáy (First 3 Cross Edges):</strong> Tìm và ghép 3 cặp cạnh màu Trắng (ví dụ: Trắng-Đỏ, Trắng-Xanh lá, Trắng-Xanh dương) rồi đặt chúng vào đúng vị trí chữ thập ở đáy. Chừa lại 1 vị trí cạnh Trắng chưa giải để làm khe hở.</li>
            <li><strong>Giải quyết 4 tâm bên hông:</strong> Tận dụng khe hở và các lát xoay đôi ở giữa để giải quyết nhanh 4 tâm bên (Đỏ, Xanh lá, Cam, Xanh dương) mà không làm hỏng 3 cạnh Trắng đã khóa ở đáy.</li>
            <li><strong>Hoàn thành chữ thập đáy (Cross):</strong> Ghép cặp cạnh Trắng cuối cùng (Trắng-Cam) và đưa vào khe đáy để hoàn thành chữ thập Trắng hoàn chỉnh.</li>
            <li><strong>Ghép 8 cạnh còn lại (3-2-3 Edge Pairing):</strong> Đây là bước đột phá giúp giải quyết đồng thời nhiều cạnh.</li>
            <li><strong>Giai đoạn 3x3x3:</strong> Giải phần còn lại như khối 3x3 bình thường. Lúc này chữ thập đáy đã xong, bạn có thể đi thẳng vào F2L ngay lập tức!</li>
          </ol>
        </div>

        <h4>Kỹ thuật ghép cạnh nâng cao 3-2-3</h4>
        <p>Thay vì ghép từng cạnh riêng lẻ, ta ghép 3 cặp cạnh cùng lúc, sau đó ghép 2 cặp tiếp theo và 3 cặp cuối cùng. Thuật toán lật cạnh và chèn (Flipping & Inserting) kinh điển được dùng là:</p>
        <div class="formula-box">
          Công thức lật cạnh: <span class="formula">u' R U R' F R' F' R u</span>
        </div>
        <p><em>* Giải thích: Xoay lớp trên đôi (u') sang phải để tách cạnh, thực hiện thuật toán lật viên cạnh bên phải, rồi trả lớp trên đôi (u) về để ghép đồng thời cả 3 cặp cạnh.</em></p>
        
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="u' R U R' F R' F' R u">Chạy thuật toán lật cạnh ghép 3-2-3</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="patterns" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Các hoa văn 4x4x4 đẹp ➔</button>
      </div>

      <!-- Tab Patterns -->
      <div class="tab-content hidden" id="tab-patterns">
        <div class="tab-header-row">
          <h3>Các hoa văn đẹp trên Rubik 4x4x4</h3>
          <button class="btn-read-aloud" data-target="tab-patterns">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Từ khối Rubik 4x4x4 đã được giải hoàn chỉnh, các em có thể tạo ra các hiệu ứng hình khối cực kỳ ấn tượng nhờ kích thước lớn của nó:</p>
        
        <div class="step-guide">
          <strong>1. Hoa văn Bàn cờ xen kẽ (Alternating Checkerboard)</strong>
          <p>Tạo hiệu ứng bàn cờ tối ưu nhất trên khối 4x4. Do cấu tạo số lớp chẵn (4x4x4), việc tạo ô bàn cờ đan xen 100% trên cả 6 mặt là bất khả thi về mặt toán học. Thuật toán này sẽ tạo ô bàn cờ hoàn hảo trên 4 mặt (U, D, L, R) và hoa văn bàn cờ dạng khối đối xứng đẹp mắt trên 2 mặt còn lại (F, B).</p>
          <div class="formula-box">Công thức: <span class="formula">M2 S2 E2 Rw2 Uw2 Rw2 Fw2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="l l L L r r R R f f F F b b B B u u U U d d D D r r u u r r f f">Tạo hoa văn Bàn cờ xen kẽ 4x4</button>
        </div>

        <div class="step-guide">
          <strong>2. Hoa văn Nhụy hoa 2x2 (Flower Center 2x2)</strong>
          <p>Hoán đổi các khối tâm 2x2 sang các mặt đối diện, tạo nên một "nhụy hoa lớn" bắt mắt ở chính giữa các mặt.</p>
          <div class="formula-box">Công thức: <span class="formula">r d r' d'</span> (xoay các lát trong)</div>
          <p>Để đơn giản trực quan, ta xoay các lát đôi ở giữa:</p>
          <button class="btn btn-success play-algo-pattern" data-algo="r d r' d'">Tạo hoa văn Nhụy hoa 2x2</button>
        </div>

        <div class="step-guide">
          <strong>3. Bàn cờ viền - Lát trong (Inner Slice Checkerboard)</strong>
          <p>Xoay các lát bên trong để tạo hoa văn bàn cờ trên các cạnh của khối Rubik, trong khi các khối góc và cụm tâm 2x2 ở giữa vẫn giữ nguyên màu sắc đồng nhất.</p>
          <div class="formula-box">Công thức: <span class="formula">r2 l2 u2 d2 f2 b2</span> (xoay các lát trong)</div>
          <button class="btn btn-success play-algo-pattern" data-algo="r r R R l l L L u u U U d d D D f f F F b b B B">Tạo hoa văn Bàn cờ viền trong 4x4</button>
        </div>

        <div class="step-guide">
          <strong>4. Khối 3x3x3 lồng trong 4x4x4 (3x3 in a 4x4)</strong>
          <p>Tạo ảo giác có một khối Rubik 3x3x3 bị kẹt ở góc của khối 4x4x4. Công thức này chỉ sử dụng các nước xoay lớp ngoài cùng.</p>
          <div class="formula-box">Công thức: <span class="formula">F L F U' R U F2 L2 U' L' B D' B' L2 U</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F L F U' R U F2 L2 U' L' B D' B' L2 U">Tạo hoa văn 3x3 lồng 4x4</button>
        </div>

        <div class="step-guide">
          <strong>5. Khối 2x2x2 lồng trong 4x4x4 (2x2 in a 4x4)</strong>
          <p>Tạo ra một khối Rubik 2x2x2 nhỏ xíu nằm lọt thỏm ở một góc. Công thức này sử dụng các bước xoay kép (Wide moves).</p>
          <div class="formula-box">Công thức: <span class="formula">Fw Lw Fw Uw' Rw Uw Fw2 Lw2 Uw' Lw' Bw Dw' Bw' Lw2 Uw</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="f l f u' r u f f l l u' l' b d' b' l l u">Tạo hoa văn 2x2 lồng 4x4</button>
        </div>

        <div class="step-guide">
          <strong>6. Ba khối lồng nhau (Triple Cube in a Cube)</strong>
          <p>Sự kết hợp hoàn hảo để tạo ra hiệu ứng: Khối 2x2x2 nằm trong khối 3x3x3, và khối 3x3x3 lại nằm trong khối 4x4x4! Các em chỉ cần thực hiện liên tiếp công thức (5) rồi đến công thức (4).</p>
          <div class="formula-box">Công thức: <span class="formula">(Fw Lw Fw Uw' Rw Uw Fw2 Lw2 Uw' Lw' Bw Dw' Bw' Lw2 Uw) + (F L F U' R U F2 L2 U' L' B D' B' L2 U)</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="f l f u' r u f f l l u' l' b d' b' l l u F L F U' R U F F L L U' L' B D' B' L L U">Tạo hoa văn Ba khối lồng nhau</button>
        </div>

        <div class="step-guide">
          <strong>7. Bàn cờ viền - Lớp ngoài (Outer Slice Checkerboard)</strong>
          <p>Xoay các lớp ngoài cùng để hoán đổi các cặp cạnh, tạo hiệu ứng viền bàn cờ tương tự như kiểu xoay lát trong nhưng ở hướng đối lập (các khối góc và cụm tâm 2x2 vẫn giữ nguyên màu gốc).</p>
          <div class="formula-box">Công thức: <span class="formula">R2 L2 U2 D2 F2 B2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="R R L L U U D D F F B B">Tạo hoa văn Bàn cờ viền ngoài 4x4</button>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
// Javascript Rubik's Cube 4x4x4 3D Engine & Logic
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
  let animSpeedMs = 400; 
  let animPauseMs = 100;
  
  // Drag rotation
  let cubeRx = -25;
  let cubeRy = 45;
  let isDragging = false;
  let startX = 0;
  let startY = 0;
  
  // Generate initial state of 64 cubies for 4x4x4
  function initCube() {
    cubies = [];
    cubeEl.innerHTML = '';
    
    // Coordinates range over [-1.5, -0.5, 0.5, 1.5]
    const coords = [-1.5, -0.5, 0.5, 1.5];
    
    for (let x of coords) {
      for (let y of coords) {
        for (let z of coords) {
          const faces = {
            u: (y === -1.5) ? defaultColors.u : defaultColors.internal,
            d: (y === 1.5) ? defaultColors.d : defaultColors.internal,
            l: (x === -1.5) ? defaultColors.l : defaultColors.internal,
            r: (x === 1.5) ? defaultColors.r : defaultColors.internal,
            f: (z === 1.5) ? defaultColors.f : defaultColors.internal,
            b: (z === -1.5) ? defaultColors.b : defaultColors.internal
          };
          
          // Generate unique ID
          const id = `cubie-${x.toString().replace('.', '_')}-${y.toString().replace('.', '_')}-${z.toString().replace('.', '_')}`;
          cubies.push({ x, y, z, faces, id });
        }
      }
    }
    renderCube();
  }
  
  // Render cube state into DOM elements
  function renderCube() {
    cubeEl.innerHTML = '';
    cubies.forEach(c => {
      const cubieDiv = document.createElement('div');
      cubieDiv.className = 'cubie';
      cubieDiv.id = c.id;
      cubieDiv.style.setProperty('--x', c.x);
      cubieDiv.style.setProperty('--y', c.y);
      cubieDiv.style.setProperty('--z', c.z);
      
      const faceDirections = ['u', 'd', 'l', 'r', 'f', 'b'];
      faceDirections.forEach(dir => {
        const faceDiv = document.createElement('div');
        faceDiv.className = `face ${dir} ${c.faces[dir]}`;
        cubieDiv.appendChild(faceDiv);
      });
      
      cubeEl.appendChild(cubieDiv);
    });
  }
  
  // Execute a rotation move for 4x4x4
  function makeMove(moveCode, callback) {
    if (isAnimating) {
      moveQueue.push({ moveCode, callback });
      return;
    }
    
    // Play turn sound
    try {
      playTurnSound(Math.min(400, animSpeedMs));
    } catch (e) {}
    
    isAnimating = true;
    const isPrime = moveCode.includes("'");
    const baseMove = moveCode.replace("'", "");
    
    let filterFunc;
    let rotationAxis;
    let cssAngle;
    let logicalDir = isPrime ? 'CCW' : 'CW';
    
    // Coordinates checks:
    // Outer layers: 1.5 / -1.5
    // Double layers (lowercase): >= 0.5 or <= -0.5
    switch (baseMove) {
      // Outer turns
      case 'R':
        filterFunc = c => c.x === 1.5;
        rotationAxis = 'X';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'L':
        filterFunc = c => c.x === -1.5;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'U':
        filterFunc = c => c.y === -1.5;
        rotationAxis = 'Y';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'D':
        filterFunc = c => c.y === 1.5;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'F':
        filterFunc = c => c.z === 1.5;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'B':
        filterFunc = c => c.z === -1.5;
        rotationAxis = 'Z';
        cssAngle = isPrime ? 90 : -90;
        break;
        
      // Double layer turns
      case 'r':
        filterFunc = c => c.x >= 0.5;
        rotationAxis = 'X';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'l':
        filterFunc = c => c.x <= -0.5;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'u':
        filterFunc = c => c.y <= -0.5;
        rotationAxis = 'Y';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'd':
        filterFunc = c => c.y >= 0.5;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'f':
        filterFunc = c => c.z >= 0.5;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'b':
        filterFunc = c => c.z <= -0.5;
        rotationAxis = 'Z';
        cssAngle = isPrime ? 90 : -90;
        break;
        
      default:
        isAnimating = false;
        if (callback) callback();
        return;
    }
    
    const rotatingCubies = cubies.filter(filterFunc);
    
    // Create temp animation wrapper
    const wrapper = document.createElement('div');
    wrapper.className = 'rotation-wrapper';
    cubeEl.appendChild(wrapper);
    
    rotatingCubies.forEach(c => {
      const el = document.getElementById(c.id);
      if (el) wrapper.appendChild(el);
    });
    
    wrapper.style.transition = `transform ${animSpeedMs / 1000}s cubic-bezier(0.25, 1, 0.5, 1)`;
    wrapper.offsetHeight; // force reflow
    
    wrapper.style.transform = `rotate${rotationAxis}(${cssAngle}deg)`;
    
    setTimeout(() => {
      // Coordinate and face states math update
      rotatingCubies.forEach(c => {
        let x = c.x;
        let y = c.y;
        let z = c.z;
        let temp;
        
        if (rotationAxis === 'X') {
          if (baseMove === 'R' || baseMove === 'r') {
            if (logicalDir === 'CW') {
              c.y = -z; c.z = y;
              temp = c.faces.u; c.faces.u = c.faces.f; c.faces.f = c.faces.d; c.faces.d = c.faces.b; c.faces.b = temp;
            } else {
              c.y = z; c.z = -y;
              temp = c.faces.u; c.faces.u = c.faces.b; c.faces.b = c.faces.d; c.faces.d = c.faces.f; c.faces.f = temp;
            }
          } else { // L or l
            if (logicalDir === 'CW') {
              c.y = z; c.z = -y;
              temp = c.faces.u; c.faces.u = c.faces.b; c.faces.b = c.faces.d; c.faces.d = c.faces.f; c.faces.f = temp;
            } else {
              c.y = -z; c.z = y;
              temp = c.faces.u; c.faces.u = c.faces.f; c.faces.f = c.faces.d; c.faces.d = c.faces.b; c.faces.b = temp;
            }
          }
        } else if (rotationAxis === 'Y') {
          if (baseMove === 'U' || baseMove === 'u') {
            if (logicalDir === 'CW') {
              c.x = -z; c.z = x;
              temp = c.faces.l; c.faces.l = c.faces.f; c.faces.f = c.faces.r; c.faces.r = c.faces.b; c.faces.b = temp;
            } else {
              c.x = z; c.z = -x;
              temp = c.faces.l; c.faces.l = c.faces.b; c.faces.b = c.faces.r; c.faces.r = c.faces.f; c.faces.f = temp;
            }
          } else { // D or d
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
          } else { // B or b
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
      
      // Update ID based on final rounded coordinates to prevent lookup drift
      rotatingCubies.forEach(c => {
        c.id = `cubie-${c.x.toString().replace('.', '_')}-${c.y.toString().replace('.', '_')}-${c.z.toString().replace('.', '_')}`;
      });
      
      renderCube();
      
      if (wrapper.parentNode) {
        wrapper.parentNode.removeChild(wrapper);
      }
      
      isAnimating = false;
      if (callback) callback();
      
      if (moveQueue.length > 0) {
        const next = moveQueue.shift();
        makeMove(next.moveCode, next.callback);
      }
    }, animSpeedMs + animPauseMs);
  }
  
  // Play algorithms sequentially
  function playAlgorithm(algoString, onComplete) {
    const prevSpeed = animSpeedMs;
    const prevPause = animPauseMs;
    
    animSpeedMs = 1200;
    animPauseMs = 400;
    
    const moves = algoString.split(/\s+/).filter(m => m.trim().length > 0);
    const parsedMoves = [];
    
    moves.forEach(m => {
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
        animSpeedMs = prevSpeed;
        animPauseMs = prevPause;
        if (onComplete) onComplete();
      }
    }
    doNext();
  }
  
  // Scramble the 4x4 cube
  function scrambleCube() {
    if (isAnimating) return;
    hudStatus.innerText = "Đang xáo trộn ngẫu nhiên...";
    
    animSpeedMs = 180;
    animPauseMs = 40;
    
    const moveOptions = [
      'U', 'D', 'L', 'R', 'F', 'B', "U'", "D'", "L'", "R'", "F'", "B'",
      'u', 'd', 'l', 'r', 'f', 'b', "u'", "d'", "l'", "r'", "f'", "b'"
    ];
    let count = 25;
    
    function scrambleStep() {
      if (count > 0) {
        const randomMove = moveOptions[Math.floor(Math.random() * moveOptions.length)];
        count--;
        makeMove(randomMove, scrambleStep);
      } else {
        hudStatus.innerText = "Đã xáo trộn xong! Nhấn kéo để xoay góc nhìn hoặc thực hiện các bước giải.";
        animSpeedMs = 400;
        animPauseMs = 100;
      }
    }
    scrambleStep();
  }
  
  initCube();
  
  document.getElementById('btn-scramble').addEventListener('click', scrambleCube);
  document.getElementById('btn-reset').addEventListener('click', () => {
    if (isAnimating) return;
    initCube();
    hudStatus.innerText = "Khối Rubik 4x4x4 đã được khôi phục về trạng thái ban đầu.";
  });
  
  document.querySelectorAll('.move-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      const mv = btn.getAttribute('data-move');
      if (mv && !isAnimating) {
        hudStatus.innerText = `Xoay nước: ${mv}`;
        makeMove(mv);
      }
    });
  });
  
  document.addEventListener('click', e => {
    if (e.target && e.target.classList.contains('play-algo')) {
      const algo = e.target.getAttribute('data-algo');
      if (algo) {
        playAlgorithm(algo);
      }
    }
    if (e.target && e.target.classList.contains('play-algo-pattern')) {
      const algo = e.target.getAttribute('data-algo');
      if (algo && !isAnimating) {
        initCube();
        hudStatus.innerText = "Khôi phục khối Rubik sạch trước khi tạo hoa văn...";
        setTimeout(() => {
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
  
  // 3D drag logic
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
    cubeRx = Math.max(-60, Math.min(60, cubeRx));
    
    startX = clientX;
    startY = clientY;
    setCubeRotation();
  };
  
  cubeEl.addEventListener('mousedown', e => {
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
  
  // Touch support
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
      try {
        stopSpeech();
      } catch (e) {}
      
      tabButtons.forEach(b => b.classList.remove('active'));
      tabContents.forEach(c => c.classList.add('hidden'));
      
      btn.classList.add('active');
      const step = btn.getAttribute('data-step');
      const content = document.getElementById(`tab-${step}`);
      if (content) {
        content.classList.remove('hidden');
      }
    });
  });
  
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

  // --- Audio / TTS Helper Code ---
  let audioCtx = null;
  
  function initAudio() {
    if (!audioCtx) {
      audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (audioCtx.state === 'suspended') {
      audioCtx.resume();
    }
  }

  let noiseBuffer = null;
  function getNoiseBuffer() {
    if (noiseBuffer) return noiseBuffer;
    initAudio();
    const bufferSize = audioCtx.sampleRate * 2;
    const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
    const data = buffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) {
      data[i] = Math.random() * 2 - 1;
    }
    noiseBuffer = buffer;
    return noiseBuffer;
  }

  function playTurnSound(durationMs) {
    try {
      initAudio();
      if (!audioCtx) return;
      
      const now = audioCtx.currentTime;
      const durationSec = durationMs / 1000;
      
      const noiseNode = audioCtx.createBufferSource();
      noiseNode.buffer = getNoiseBuffer();
      
      const filter = audioCtx.createBiquadFilter();
      filter.type = 'bandpass';
      filter.frequency.setValueAtTime(1200, now);
      filter.frequency.exponentialRampToValueAtTime(700, now + durationSec);
      filter.Q.setValueAtTime(3, now);
      
      const noiseGain = audioCtx.createGain();
      noiseGain.gain.setValueAtTime(0.08, now);
      noiseGain.gain.exponentialRampToValueAtTime(0.001, now + durationSec);
      
      noiseNode.connect(filter);
      filter.connect(noiseGain);
      noiseGain.connect(audioCtx.destination);
      
      const osc = audioCtx.createOscillator();
      const oscGain = audioCtx.createGain();
      
      osc.type = 'triangle';
      osc.frequency.setValueAtTime(100, now + durationSec - 0.05);
      osc.frequency.exponentialRampToValueAtTime(30, now + durationSec);
      
      oscGain.gain.setValueAtTime(0.0, now);
      oscGain.gain.setValueAtTime(0.0, now + durationSec - 0.05);
      oscGain.gain.linearRampToValueAtTime(0.15, now + durationSec - 0.04);
      oscGain.gain.exponentialRampToValueAtTime(0.001, now + durationSec);
      
      osc.connect(oscGain);
      oscGain.connect(audioCtx.destination);
      
      noiseNode.start(now);
      noiseNode.stop(now + durationSec);
      
      osc.start(now + durationSec - 0.05);
      osc.stop(now + durationSec);
    } catch (e) {
      console.warn("Web Audio turn sound failed: ", e);
    }
  }

  let activeUtterance = null;
  let activeSpeechButton = null;

  function stopSpeech() {
    if (window.speechSynthesis) {
      window.speechSynthesis.cancel();
    }
    if (activeSpeechButton) {
      activeSpeechButton.innerHTML = "Đọc hướng dẫn 🔊";
      activeSpeechButton.classList.remove('active');
      activeSpeechButton = null;
    }
    activeUtterance = null;
  }

  function speakTab(tabId, buttonEl) {
    try {
      initAudio();
    } catch (e) {}
    
    if (activeUtterance) {
      const wasActive = (activeSpeechButton === buttonEl);
      stopSpeech();
      if (wasActive) {
        return;
      }
    }
    
    const tabEl = document.getElementById(tabId);
    if (!tabEl) return;
    
    function getCleanText(node) {
      let text = "";
      node.childNodes.forEach(child => {
        if (child.nodeType === Node.TEXT_NODE) {
          text += child.textContent;
        } else if (child.nodeType === Node.ELEMENT_NODE) {
          if (child.classList.contains('formula-box') || 
              child.classList.contains('btn') || 
              child.classList.contains('formula') || 
              child.tagName === 'BUTTON' || 
              child.classList.contains('tab-header-row')) {
            return;
          }
          text += getCleanText(child) + " ";
        }
      });
      return text;
    }
    
    let rawText = getCleanText(tabEl);
    let cleanText = rawText.replace(/\s+/g, ' ').trim();
    
    if (!cleanText) return;
    
    const utterance = new SpeechSynthesisUtterance(cleanText);
    utterance.lang = 'vi-VN';
    utterance.rate = 0.95;
    utterance.pitch = 1.0;
    
    if (window.speechSynthesis) {
      const voices = window.speechSynthesis.getVoices();
      const viVoice = voices.find(v => v.lang.includes('vi') || v.lang.includes('VI'));
      if (viVoice) {
        utterance.voice = viVoice;
      }
    }
    
    utterance.onstart = () => {
      buttonEl.innerHTML = "Dừng đọc ⏹";
      buttonEl.classList.add('active');
      activeSpeechButton = buttonEl;
      activeUtterance = utterance;
    };
    
    utterance.onend = () => {
      if (activeSpeechButton === buttonEl) {
        buttonEl.innerHTML = "Đọc hướng dẫn 🔊";
        buttonEl.classList.remove('active');
        activeSpeechButton = null;
        activeUtterance = null;
      }
    };
    
    utterance.onerror = (e) => {
      console.warn("Speech Synthesis error: ", e);
      if (activeSpeechButton === buttonEl) {
        buttonEl.innerHTML = "Đọc hướng dẫn 🔊";
        buttonEl.classList.remove('active');
        activeSpeechButton = null;
        activeUtterance = null;
      }
    };
    
    window.speechSynthesis.speak(utterance);
  }

  document.querySelectorAll('.btn-read-aloud').forEach(btn => {
    btn.addEventListener('click', (e) => {
      e.stopPropagation();
      const targetId = btn.getAttribute('data-target');
      speakTab(targetId, btn);
    });
  });

  document.body.addEventListener('click', () => {
    try {
      initAudio();
    } catch (e) {}
  }, { once: true });
  
})();
</script>
