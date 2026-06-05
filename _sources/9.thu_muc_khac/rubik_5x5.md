# Hướng dẫn chơi Rubik 5x5x5

Ứng dụng tương tác 3D và hướng dẫn giải Rubik 5x5x5 (Professor's Cube) theo phương pháp rút gọn (Reduction Method) cực kỳ dễ hiểu của thầy Huy, giúp các em học sinh dễ dàng làm quen và tự giải được khối Rubik 5x5x5 tại nhà.

```{raw} html
<style>
/* Modern premium glassmorphism styling for Rubik 5x5 tutorial app */
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');

.rubik-app-wrapper {
  font-family: 'Outfit', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  color: #f3f4f6;
  background: radial-gradient(circle at top left, #1c1829, #0a0812);
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
  background: linear-gradient(135deg, #f472b6, #a78bfa);
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
  width: 260px;
  height: 260px;
  perspective: 600px;
  margin: 20px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  user-select: none;
}

.cube {
  position: relative;
  width: 140px;
  height: 140px;
  transform-style: preserve-3d;
  transform: rotateX(-25deg) rotateY(45deg);
  cursor: grab;
  transition: transform 0.1s ease-out;
}

.cube:active {
  cursor: grabbing;
}

/* 5x5 Cubie pieces */
.cubie {
  position: absolute;
  width: 24px;
  height: 24px;
  left: 58px;
  top: 58px;
  transform-style: preserve-3d;
  transform: translate3d(calc(var(--x) * 26px), calc(var(--y) * 26px), calc(var(--z) * 26px));
}

.cubie.animating {
  transition: transform 0.4s ease-in-out;
}

.face {
  position: absolute;
  width: 23px;
  height: 23px;
  background-color: #121214;
  border: 1px solid #08080a;
  border-radius: 3.5px;
  box-sizing: border-box;
  display: flex;
  justify-content: center;
  align-items: center;
  backface-visibility: visible;
}

.face::after {
  content: '';
  width: 18px;
  height: 18px;
  border-radius: 2.5px;
  box-shadow: inset 0 1px 2px rgba(255, 255, 255, 0.3), inset 0 -1px 2px rgba(0, 0, 0, 0.3);
  transition: background 0.3s ease;
  box-sizing: border-box;
}

/* 3D face transforms for 5x5 */
.face.u { transform: rotateX(90deg) translateZ(12px); }
.face.d { transform: rotateX(-90deg) translateZ(12px); }
.face.l { transform: rotateY(-90deg) translateZ(12px); }
.face.r { transform: rotateY(90deg) translateZ(12px); }
.face.f { transform: rotateY(0deg) translateZ(12px); }
.face.b { transform: rotateY(180deg) translateZ(12px); }

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
  background: linear-gradient(135deg, #ec4899, #8b5cf6);
  border: none;
  flex: 1;
}

.btn-primary:hover:not(:disabled) {
  background: linear-gradient(135deg, #f472b6, #a78bfa);
  box-shadow: 0 0 12px rgba(236, 72, 153, 0.4);
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
  color: #f472b6;
  background: rgba(255, 255, 255, 0.05);
}

.tab-btn.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: #ec4899;
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
  background: rgba(244, 114, 182, 0.15);
  border: 1px solid rgba(244, 114, 182, 0.35);
  color: #f9a8d4;
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
  background: rgba(244, 114, 182, 0.3);
  box-shadow: 0 0 10px rgba(244, 114, 182, 0.15);
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
  border-left: 4px solid #ec4899;
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
  color: #f9a8d4;
  word-break: break-all;
}

.formula {
  background: rgba(236, 72, 153, 0.2);
  padding: 2px 6px;
  border-radius: 4px;
  color: #f472b6;
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
  background: rgba(244, 114, 182, 0.1);
  border: 1px solid rgba(244, 114, 182, 0.2);
  color: #f9a8d4;
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
  width: 140px;
  height: 140px;
  left: 0;
  top: 0;
  transform-style: preserve-3d;
}
</style>

<div class="rubik-app-wrapper">
  <div class="rubik-app-title">Trải nghiệm Giải Rubik 5x5x5 3D</div>
  
  <div class="cube-workspace">
    <!-- Left Panel: 3D view and controls -->
    <div class="cube-left-panel">
      <div class="hud-info" id="hud-status">Kéo thả chuột để xoay khối 5x5x5. Sẵn sàng!</div>
      
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
        <button class="tab-btn" data-step="step1">1. Giải 6 Tâm (Centers)</button>
        <button class="tab-btn" data-step="step2">2. Ghép Cạnh (Edges)</button>
        <button class="tab-btn" data-step="step3">3. Giải như 3x3</button>
        <button class="tab-btn" data-step="parity">4. Lỗi Parity 5x5</button>
        <button class="tab-btn" data-step="patterns">🎨 Hoa văn 5x5</button>
      </div>
      
      <!-- Tab Intro -->
      <div class="tab-content" id="tab-intro">
        <div class="tab-header-row">
          <h3>Tổng quan cấu tạo Rubik 5x5x5</h3>
          <button class="btn-read-aloud" data-target="tab-intro">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Khối Rubik 5x5x5 (còn gọi là Professor's Cube) có cấu tạo gồm 150 viên nhỏ ở bề mặt bên ngoài:</p>
        <ul>
          <li><strong>54 viên tâm (Center):</strong> Mỗi mặt có cụm tâm 3x3 gồm 9 viên. Khác với 4x4, viên chính giữa của cụm tâm 5x5 **là cố định** giống 3x3. Do đó, hệ màu của 5x5 là cố định và không sợ bị giải sai thứ tự mặt!</li>
          <li><strong>36 viên cạnh (Edge):</strong> Gồm 12 nhóm cạnh. Mỗi nhóm cạnh của khối được cấu tạo từ 3 viên riêng biệt (1 viên giữa và 2 viên hông).</li>
          <li><strong>8 viên góc (Corner):</strong> Có 3 màu sắc, nằm ở các góc của khối (hoàn toàn giống Rubik 3x3).</li>
        </ul>
        
        <h4>Thứ tự màu sắc cố định tiêu chuẩn</h4>
        <p>Cầm khối Rubik sao cho mặt **Trắng** ở đáy và mặt **Vàng** ở trên đỉnh. Thứ tự các mặt xung quanh theo chiều kim đồng hồ sẽ là:</p>
        <div class="step-guide">
          <strong>Đỏ (Trước) ➔ Xanh lá (Phải) ➔ Cam (Sau) ➔ Xanh dương (Trái)</strong>
        </div>

        <h4>Ký hiệu xoay 5x5 tiêu chuẩn</h4>
        <div class="notation-grid">
          <div><strong>R / L / U / D / F / B:</strong> Xoay 1 lớp ngoài cùng tương ứng</div>
          <div><strong>Rw / Lw / Uw / Dw / Fw / Bw:</strong> Xoay đồng thời cả 2 lớp ngoài cùng sát nhau (trên hình 3D ký hiệu là chữ thường: r, l, u, d, f, b)</div>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step1" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 1 (Giải 6 Tâm) ➔</button>
      </div>

      <!-- Tab 1 -->
      <div class="tab-content hidden" id="tab-step1">
        <div class="tab-header-row">
          <h3>Bước 1: Giải cụm tâm 3x3 (Centers)</h3>
          <button class="btn-read-aloud" data-target="tab-step1">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Mục tiêu: Đưa các viên tâm đơn lẻ về đúng mặt để tạo thành 6 cụm tâm 3x3 đồng màu.</p>
        
        <div class="step-guide">
          <strong>Quy trình thực hiện khuyến nghị của Thầy Huy:</strong>
          <ol>
            <li><strong>Tạo thanh 1x3:</strong> Ghép 1 viên tâm chính giữa với 2 viên tâm cạnh để tạo thành một thanh 1x3 hoàn chỉnh.</li>
            <li><strong>Ghép cụm:</strong> Tạo tiếp 2 thanh 1x3 hông rồi ghép chúng vào hai bên thanh giữa để hoàn thành cụm tâm 3x3.</li>
            <li><strong>Trình tự giải mặt:</strong> Giải quyết tâm **Trắng** trước ➔ đến tâm **Vàng** đối diện ➔ giải tiếp 3 tâm bên hông bất kỳ ➔ cuối cùng giải quyết 2 tâm còn lại bằng cách hoán đổi các thanh qua lại.</li>
          </ol>
        </div>
        <div class="algo-player">
          <p>Thuật toán đưa thanh tâm vàng lên đỉnh mà không làm hỏng tâm trắng đáy:</p>
          <div class="formula-box">Công thức đưa thanh: <span class="formula">Rw U2 Rw'</span></div>
          <button class="btn btn-success play-algo" data-algo="r U U r'">Chạy thử thuật toán đưa thanh (Rw U2 Rw')</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step2" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 2 (Ghép Cạnh) ➔</button>
      </div>

      <!-- Tab 2 -->
      <div class="tab-content hidden" id="tab-step2">
        <div class="tab-header-row">
          <h3>Bước 2: Ghép bộ ba viên cạnh (Edge Pairing)</h3>
          <button class="btn-read-aloud" data-target="tab-step2">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Mục tiêu: Tìm các viên cạnh cùng màu và ghép chúng thành một cụm cạnh thống nhất gồm 3 viên (1 viên giữa và 2 viên bên).</p>
        
        <div class="step-guide">
          <strong>Phương pháp ghép cạnh tự do (Free-Slicing):</strong>
          <ol>
            <li>Xoay lệch tầng giữa (`Uw` hoặc `Dw`) để chuẩn bị ghép viên cạnh hông vào viên cạnh giữa.</li>
            <li>Đưa một viên cạnh chưa giải ở mặt trên xuống thay thế vị trí của viên cạnh vừa được ghép.</li>
            <li>Trả lát cắt tầng giữa (`Uw'` hoặc `Dw'`) về để khôi phục lại các tâm đã giải.</li>
          </ol>
          <p>Trong quá trình ghép, bạn sẽ cần thuật toán lật viên cạnh tại chỗ để xếp đúng chiều màu:</p>
          <div class="formula-box">
            Công thức lật cạnh: <span class="formula">R U R' F R' F' R</span>
          </div>
        </div>
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="R U R' F R' F' R">Chạy thuật toán lật cạnh (R U R' F R' F' R)</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="step3" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 3 (Giải như 3x3) ➔</button>
      </div>

      <!-- Tab 3 -->
      <div class="tab-content hidden" id="tab-step3">
        <div class="tab-header-row">
          <h3>Bước 3: Giải tương đương khối 3x3x3</h3>
          <button class="btn-read-aloud" data-target="tab-step3">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Sau khi giải xong 6 cụm tâm 3x3 và ghép xong cả 12 bộ ba viên cạnh, khối Rubik 5x5x5 của bạn giờ đây đã được đưa về trạng thái tương đương với một khối Rubik 3x3x3:</p>
        <ul>
          <li>Cụm tâm 3x3 ở giữa hoạt động như 1 viên tâm cố định của 3x3.</li>
          <li>Bộ ba viên cạnh đã ghép đôi hoạt động như 1 viên cạnh của 3x3.</li>
          <li>8 viên góc giữ nguyên vai trò.</li>
        </ul>
        <div class="step-guide">
          Hãy áp dụng toàn bộ phương pháp giải 3x3 thông thường để hoàn thiện khối Rubik. Nếu gặp trường hợp viên cạnh cuối bị lật ngược, đó là lỗi Parity đặc biệt của khối 5x5x5. Hãy chuyển sang Tab tiếp theo để xem công thức xử lý.
        </div>
        <button class="btn btn-primary next-step-btn" data-next="parity" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Bước 4 (Lỗi Parity) ➔</button>
      </div>

      <!-- Tab Parity -->
      <div class="tab-content hidden" id="tab-parity">
        <div class="tab-header-row">
          <h3>Khắc phục lỗi lật cạnh cuối (5x5 Parity)</h3>
          <button class="btn-read-aloud" data-target="tab-parity">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Trên khối 5x5x5, bạn sẽ gặp tình huống duy nhất là cặp cạnh cuối cùng đã ghép đúng vị trí nhưng viên cạnh giữa lại bị lật màu ngược với hai viên cạnh hông (hoặc ngược lại).</p>
        
        <div class="step-guide">
          <strong>Cách xử lý:</strong> Hướng bộ cạnh bị lỗi về mặt **Trước (Front) - Trên (Up)** và thực hiện công thức Parity kinh điển sau:
          <div class="formula-box" style="font-size: 0.95rem; line-height: 1.6;">
            Công thức Parity 5x5:<br>
            <span class="formula">Rw2 B2 U2 Lw U2 Rw' U2 Rw U2 F2 Rw F2 Lw' B2 Rw2</span>
          </div>
          <p><em>* Lưu ý: Ký hiệu viết thường Rw, Lw tương ứng với việc xoay cả 2 lớp ngoài cùng bên Phải/Trái (trong hình 3D kí hiệu là r, l).</em></p>
        </div>
        <div class="algo-player">
          <button class="btn btn-success play-algo" data-algo="r r B B U U l U U r' U U r U U F F r F F l' B B r r">Chạy thuật toán sửa lỗi Parity 5x5</button>
        </div>
        <button class="btn btn-primary next-step-btn" data-next="patterns" style="width: 100%; padding: 12px; margin-top: 15px;">Tiếp tục: Xem các hoa văn 5x5 đẹp ➔</button>
      </div>

      <!-- Tab Patterns -->
      <div class="tab-content hidden" id="tab-patterns">
        <div class="tab-header-row">
          <h3>Các hoa văn nghệ thuật trên Rubik 5x5x5</h3>
          <button class="btn-read-aloud" data-target="tab-patterns">Đọc hướng dẫn 🔊</button>
        </div>
        <p>Kích thước lớn của khối 5x5x5 cho phép tạo ra rất nhiều hiệu ứng hình khối lồng nhau và bàn cờ đan xen tuyệt đẹp:</p>
        
        <div class="step-guide">
          <strong>1. Bàn cờ nhụy thập (Center-Cross Checkerboard)</strong>
          <p>Xoay các lớp đôi bên trong để tạo ra hoa văn bàn cờ tuyệt đẹp chạy dọc các cạnh hông, trong khi lõi chữ thập ở giữa mỗi mặt vẫn giữ nguyên màu gốc.</p>
          <div class="formula-box">Công thức: <span class="formula">Rw2 Lw2 Uw2 Dw2 Fw2 Bw2</span> (xoay các lớp đôi)</div>
          <button class="btn btn-success play-algo-pattern" data-algo="r r l l u u d d f f b b">Tạo hoa văn Bàn cờ nhụy thập</button>
        </div>

        <div class="step-guide">
          <strong>2. Hoa văn Nhụy hoa lớn 3x3 (3x3 Flower Center)</strong>
          <p>Hoán đổi toàn bộ cụm tâm 3x3 sang các mặt đối diện, tạo nên một bông hoa rực rỡ ở chính giữa mỗi mặt khối Rubik.</p>
          <div class="formula-box">Công thức: <span class="formula">Rw Dw Rw' Dw'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="r d r' d'">Tạo hoa văn Nhụy hoa lớn 3x3</button>
        </div>

        <div class="step-guide">
          <strong>3. Khối 4x4x4 lồng trong 5x5x5 (4x4 in a 5x5)</strong>
          <p>Tạo ảo giác có một khối Rubik 4x4x4 xoắn lệch tầng nằm lọt vào góc của khối 5x5x5.</p>
          <div class="formula-box">Công thức: <span class="formula">Fw Lw Fw Uw' Rw Uw Fw2 Lw2 Uw' Lw' Bw Dw' Bw' Lw2 Uw</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="f l f u' r u f f l l u' l' b d' b' l l u">Tạo hoa văn 4x4 lồng 5x5</button>
        </div>

        <div class="step-guide">
          <strong>4. Khối 3x3x3 lồng trong 5x5x5 (3x3 in a 5x5)</strong>
          <p>Tạo hiệu ứng một khối Rubik 3x3x3 nhỏ nằm ở một góc chéo của khối 5x5x5 (chỉ sử dụng các nước xoay lớp ngoài).</p>
          <div class="formula-box">Công thức: <span class="formula">F L F U' R U F2 L2 U' L' B D' B' L2 U</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F L F U' R U F F L L U' L' B D' B' L L U">Tạo hoa văn 3x3 lồng 5x5</button>
        </div>

        <div class="step-guide">
          <strong>5. Ba khối lồng nhau (Triple Nested Cube 5x5)</strong>
          <p>Hiệu ứng tuyệt tác: Khối 3x3x3 nằm trong khối 4x4x4, và khối 4x4x4 lại nằm gọn trong khối 5x5x5!</p>
          <div class="formula-box">Công thức: <span class="formula">Chạy liên tiếp công thức (3) rồi đến công thức (4)</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="f l f u' r u f f l l u' l' b d' b' l l u F L F U' R U F F L L U' L' B D' B' L L U">Tạo hoa văn Ba khối lồng nhau</button>
        </div>

        <div class="step-guide">
          <strong>6. Bàn cờ viền ngoài (Outer Slice Checkerboard)</strong>
          <p>Xoay các lớp ngoài cùng tạo ra một viền ngoài bàn cờ đan xen màu sắc cực kỳ bắt mắt, giữ nguyên phần tâm chữ thập lớn.</p>
          <div class="formula-box">Công thức: <span class="formula">R2 L2 U2 D2 F2 B2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="R R L L U U D D F F B B">Tạo hoa văn Bàn cờ viền ngoài</button>
        </div>

        <div class="step-guide">
          <strong>7. Bàn cờ toàn phần (Full Checkerboard)</strong>
          <p>Tạo hiệu ứng bàn cờ đan xen hoàn toàn giữa tất cả các hàng dọc và ngang trên cả 6 mặt khối Rubik.</p>
          <div class="formula-box">Công thức: <span class="formula">Rw2 Lw2 Uw2 Dw2 Fw2 Bw2 + R2 L2 U2 D2 F2 B2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="r r l l u u d d f f b b R R L L U U D D F F B B">Tạo hoa văn Bàn cờ toàn phần 5x5</button>
        </div>

        <div class="step-guide">
          <strong>8. Hoa văn Rắn Anaconda (Anaconda Snake)</strong>
          <p>Tạo hình một chú rắn dài đầy màu sắc uốn lượn liên tiếp qua cả 6 mặt khối Rubik.</p>
          <div class="formula-box">Công thức: <span class="formula">L U B' U' R L' B R' F B' D R D' F'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="L U B' U' R L' B R' F B' D R D' F'">Tạo hoa văn Rắn Anaconda</button>
        </div>

        <div class="step-guide">
          <strong>9. Hoa văn Trăn Python (Python Snake)</strong>
          <p>Tương tự Anaconda nhưng tạo đường xoắn lớn đầy đặn và cân đối đặc trưng bao quanh thân khối Rubik.</p>
          <div class="formula-box">Công thức: <span class="formula">F2 R' B' U R' L F' D L' D2 L2 F2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F F R' B' U R' L F' D L' D D L L F F">Tạo hoa văn Trăn Python</button>
        </div>

        <div class="step-guide">
          <strong>10. Hoa văn Đỉnh đôi xoắn (Twisted Peaks)</strong>
          <p>Tạo hai chóp đỉnh tam giác xoắn màu ngược chiều nhau cực kỳ độc đáo và nghệ thuật ở hai đầu góc chéo.</p>
          <div class="formula-box">Công thức: <span class="formula">F D2 B R B' L' F D' L2 F2 R F' R' F2 L' F'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="F D D B R B' L' F D' L L F F R F' R' F F L' F'">Tạo hoa văn Đỉnh đôi xoắn</button>
        </div>

        <div class="step-guide">
          <strong>11. Hoa văn Hộp quà Thắt nơ (Gift Box)</strong>
          <p>Tạo các đường ruy-băng màu thắt chéo quanh khối Rubik trông như một hộp quà Giáng sinh lộng lẫy.</p>
          <div class="formula-box">Công thức: <span class="formula">U B2 R2 B2 L2 F2 R2 D' F2 L2 B F' L F2 D U' R2 F' L' R'</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="U B B R R B B L L F F R R D' F F L L B F' L F F D U' R R F' L' R'">Tạo hoa văn Hộp quà</button>
        </div>

        <div class="step-guide">
          <strong>12. Hoa văn Chữ thập lớn (Big Cross)</strong>
          <p>Hiệu ứng dấu cộng chữ thập khổng lồ đồng màu chiếm trọn tâm diện của mỗi mặt.</p>
          <div class="formula-box">Công thức: <span class="formula">R2 L' D F2 R' D' R' L U' D R D B2 R' U D2</span></div>
          <button class="btn btn-success play-algo-pattern" data-algo="R R L' D F F R' D' R' L U' D R D B B R' U D D">Tạo hoa văn Chữ thập lớn</button>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
// Javascript Rubik's Cube 5x5x5 3D Engine & Logic
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
  
  // Generate initial state of 125 cubies for 5x5x5
  function initCube() {
    cubies = [];
    cubeEl.innerHTML = '';
    
    // Coordinates range over [-2, -1, 0, 1, 2]
    const coords = [-2, -1, 0, 1, 2];
    
    for (let x of coords) {
      for (let y of coords) {
        for (let z of coords) {
          const faces = {
            u: (y === -2) ? defaultColors.u : defaultColors.internal,
            d: (y === 2) ? defaultColors.d : defaultColors.internal,
            l: (x === -2) ? defaultColors.l : defaultColors.internal,
            r: (x === 2) ? defaultColors.r : defaultColors.internal,
            f: (z === 2) ? defaultColors.f : defaultColors.internal,
            b: (z === -2) ? defaultColors.b : defaultColors.internal
          };
          
          // Generate unique ID
          const id = `cubie-${x.toString().replace('-', 'm')}-${y.toString().replace('-', 'm')}-${z.toString().replace('-', 'm')}`;
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
      // Optimisation: do not render absolute internal cubies to keep DOM lightweight
      const isInternal = Object.values(c.faces).every(f => f === defaultColors.internal);
      if (isInternal) return;
      
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
  
  // Execute a rotation move for 5x5x5
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
    // Outer layers: 2 / -2
    // Wide turns (lowercase): >= 1 or <= -1
    switch (baseMove) {
      // Outer turns
      case 'R':
        filterFunc = c => c.x === 2;
        rotationAxis = 'X';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'L':
        filterFunc = c => c.x === -2;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'U':
        filterFunc = c => c.y === -2;
        rotationAxis = 'Y';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'D':
        filterFunc = c => c.y === 2;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'F':
        filterFunc = c => c.z === 2;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'B':
        filterFunc = c => c.z === -2;
        rotationAxis = 'Z';
        cssAngle = isPrime ? 90 : -90;
        break;
        
      // Wide double-layer turns (lowercase)
      case 'r':
        filterFunc = c => c.x >= 1;
        rotationAxis = 'X';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'l':
        filterFunc = c => c.x <= -1;
        rotationAxis = 'X';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'u':
        filterFunc = c => c.y <= -1;
        rotationAxis = 'Y';
        cssAngle = isPrime ? 90 : -90;
        break;
      case 'd':
        filterFunc = c => c.y >= 1;
        rotationAxis = 'Y';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'f':
        filterFunc = c => c.z >= 1;
        rotationAxis = 'Z';
        cssAngle = isPrime ? -90 : 90;
        break;
      case 'b':
        filterFunc = c => c.z <= -1;
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
      
      // Update ID based on final coordinates
      rotatingCubies.forEach(c => {
        c.id = `cubie-${c.x.toString().replace('-', 'm')}-${c.y.toString().replace('-', 'm')}-${c.z.toString().replace('-', 'm')}`;
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
  
  // Scramble the 5x5x5 cube
  function scrambleCube() {
    if (isAnimating) return;
    hudStatus.innerText = "Đang xáo trộn ngẫu nhiên...";
    
    animSpeedMs = 180;
    animPauseMs = 40;
    
    const moveOptions = [
      'U', 'D', 'L', 'R', 'F', 'B', "U'", "D'", "L'", "R'", "F'", "B'",
      'u', 'd', 'l', 'r', 'f', 'b', "u'", "d'", "l'", "r'", "f'", "b'"
    ];
    let count = 30;
    
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
    hudStatus.innerText = "Khối Rubik 5x5x5 đã được khôi phục về trạng thái ban đầu.";
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
