// ==UserScript==
// @name        PocketBase Category Add-on
// @namespace   Violentmonkey Scripts
// @match       *://*/_/#/collections*
// @match       *://*/_/#/logs*
// @match       *://*/_/#/settings*
// @grant       none
// @version     1.2
// @author      Major Tom & GPT 4.1
// @description Adds category functionality to PocketBase sidebar
// ==/UserScript==

(function () {
	let lastUrl = location.href;
	let observer = null;
	let debounceTimer = null;
	function runScript() {
		if (document.querySelector('.pb-custom-category')) return;
		function waitSidebar() {
			const sidebar = document.querySelector(
				'.collection-sidebar .sidebar-content'
			);
			const footer = document.querySelector(
				'.collection-sidebar .sidebar-footer'
			);
			if (!sidebar || !footer) return setTimeout(waitSidebar, 500);
			const originalNewCollectionBtn = footer.querySelector('.btn');
			if (originalNewCollectionBtn) {
				originalNewCollectionBtn.style.marginBottom = '5px';
			}
			let btn = document.getElementById('pb-new-category-btn');
			if (!btn) {
				btn = document.createElement('button');
				btn.id = 'pb-new-category-btn';
				btn.type = 'button';
				btn.className = 'btn btn-block btn-outline';
				btn.innerHTML =
					'<i class="ri-folder-add-line"></i> <span class="txt">New category</span>';
				footer.appendChild(btn);
			}
			let categories = JSON.parse(
				localStorage.getItem('pb_customCategories') || '[]'
			);
			function save() {
				localStorage.setItem('pb_customCategories', JSON.stringify(categories));
			}
			function makeDraggable() {
				sidebar.querySelectorAll('.sidebar-list-item').forEach((item) => {
					const href = item.getAttribute('href');
					const match = href && href.match(/collection=([^&]+)/);
					if (match) {
						item.setAttribute('draggable', 'true');
						item.ondragstart = (e) => {
							e.dataTransfer.setData('text/plain', match[1]);
						};
					}
				});
			}
			function renderCategories() {
				// Always show all items first
				sidebar.querySelectorAll('.sidebar-list-item').forEach((item) => {
					item.style.display = '';
					item.removeAttribute('data-original-hidden');
				});
				sidebar
					.querySelectorAll('.pb-custom-category')
					.forEach((e) => e.remove());
				const insertBefore = Array.from(sidebar.children).find(
					(e) =>
						e.matches && e.matches('button[aria-label*="system collections"]')
				);
				categories.forEach((cat, idx) => {
					const catDiv = document.createElement('div');
					catDiv.className = 'pb-custom-category';
					const btnCat = document.createElement('button');
					btnCat.type = 'button';
					btnCat.className = 'sidebar-title m-b-xs link-hint';
					btnCat.setAttribute(
						'aria-label',
						(cat.expanded ? 'Collapse' : 'Expand') + ' category ' + cat.name
					);
					btnCat.setAttribute('aria-expanded', cat.expanded ? 'true' : 'false');
					btnCat.style.display = 'flex';
					btnCat.style.alignItems = 'center';
					btnCat.style.justifyContent = 'flex-start';
					btnCat.style.position = 'relative';
					const spanTxt = document.createElement('span');
					spanTxt.className = 'txt';
					spanTxt.textContent = cat.name;
					btnCat.appendChild(spanTxt);
					const icon = document.createElement('i');
					icon.className = cat.expanded
						? 'ri-arrow-up-s-line'
						: 'ri-arrow-down-s-line';
					icon.setAttribute('aria-hidden', 'true');
					icon.style.marginLeft = '5px';
					btnCat.appendChild(icon);
					const removeBtn = document.createElement('span');
					removeBtn.textContent = '✖';
					removeBtn.style.cursor = 'pointer';
					removeBtn.style.position = 'absolute';
					removeBtn.style.right = '8px';
					removeBtn.style.top = '50%';
					removeBtn.style.transform = 'translateY(-50%)';
					removeBtn.style.fontSize = '12px';
					removeBtn.title = 'Remove category';
					removeBtn.onclick = (e) => {
						e.stopPropagation();
						if (confirm('Remove this category?')) {
							categories.splice(idx, 1);
							save();
							renderCategories();
						}
					};
					btnCat.appendChild(removeBtn);
					btnCat.ondragover = (e) => e.preventDefault();
					btnCat.ondrop = (e) => {
						e.preventDefault();
						const id = e.dataTransfer.getData('text/plain');
						if (!cat.collections.includes(id)) {
							cat.collections.push(id);
							save();
							renderCategories();
						}
					};
					catDiv.appendChild(btnCat);
					const dropArea = document.createElement('div');
					dropArea.style.minHeight = '20px';
					dropArea.style.display = cat.expanded ? 'block' : 'none';
					dropArea.style.marginLeft = '10px';
					catDiv.appendChild(dropArea);
					btnCat.onclick = () => {
						cat.expanded = !cat.expanded;
						dropArea.style.display = cat.expanded ? 'block' : 'none';
						icon.className = cat.expanded
							? 'ri-arrow-up-s-line'
							: 'ri-arrow-down-s-line';
						btnCat.setAttribute(
							'aria-label',
							(cat.expanded ? 'Collapse' : 'Expand') + ' category ' + cat.name
						);
						btnCat.setAttribute(
							'aria-expanded',
							cat.expanded ? 'true' : 'false'
						);
						save();
					};
					cat.collections.forEach((id) => {
						const originalEl = sidebar.querySelector(
							`[href*="collection=${id}"]`
						);
						if (originalEl) {
							const clone = originalEl.cloneNode(true);
							clone.style.margin = '2px 0';
							clone.ondragstart = null;
							clone.setAttribute('draggable', 'false');
							dropArea.appendChild(clone);
							originalEl.style.display = 'none';
							originalEl.setAttribute('data-original-hidden', 'true');
						}
					});
					dropArea.ondragover = (e) => e.preventDefault();
					dropArea.ondrop = (e) => {
						e.preventDefault();
						const id = e.dataTransfer.getData('text/plain');
						const originalEl = sidebar.querySelector(
							`[href*="collection=${id}"]`
						);
						if (originalEl && !cat.collections.includes(id)) {
							cat.collections.push(id);
							save();
							renderCategories();
						}
					};
					if (insertBefore) {
						sidebar.insertBefore(catDiv, insertBefore);
					} else {
						sidebar.appendChild(catDiv);
					}
				});
				makeDraggable();
			}
			btn.onclick = () => {
				const name = prompt('Category name:');
				if (name) {
					categories.push({ name, collections: [], expanded: true });
					save();
					renderCategories();
					makeDraggable();
				}
			};
			sidebar.ondragover = (e) => {
				if (!e.target.closest('.pb-custom-category')) e.preventDefault();
			};
			sidebar.ondrop = (e) => {
				if (!e.target.closest('.pb-custom-category')) {
					e.preventDefault();
					const id = e.dataTransfer.getData('text/plain');
					let changed = false;
					categories.forEach((cat) => {
						const idx = cat.collections.indexOf(id);
						if (idx !== -1) {
							cat.collections.splice(idx, 1);
							changed = true;
						}
					});
					if (changed) {
						save();
						renderCategories();
					}
				}
			};
			renderCategories();
			makeDraggable();
		}
		waitSidebar();
	}
	function observeUrlAndDom() {
		setInterval(() => {
			if (location.href !== lastUrl) {
				lastUrl = location.href;
				if (location.href.includes(`/_/#/collections?`)) {
					runScript();
				}
			}
		}, 500);
		if (observer) observer.disconnect();
		observer = new MutationObserver(() => {
			if (debounceTimer) clearTimeout(debounceTimer);
			debounceTimer = setTimeout(() => {
				if (location.href.includes(`/_/#/collections?`)) {
					runScript();
				}
			}, 200);
		});
		observer.observe(document.body, { childList: true, subtree: true });
	}
	if (location.href.includes(`/_/#/collections?`)) {
		runScript();
	}
	observeUrlAndDom();
})();
