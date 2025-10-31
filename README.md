<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>學測單字卡</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .word-card {
            transition: all 0.3s ease;
        }
        .word-card:hover {
            transform: translateY(-4px);
        }
        .word-card.expanded {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .word-card.expanded * {
            color: white !important;
        }
        .dark .word-card {
            background: #2d3748;
        }
        .dark .word-card:hover {
            background: #374151;
        }
        .dark .word-card.expanded {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        @keyframes slideDown {
            from {
                opacity: 0;
                max-height: 0;
            }
            to {
                opacity: 1;
                max-height: 500px;
            }
        }
        .example-content {
            animation: slideDown 0.3s ease-out;
        }
    </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 transition-colors min-h-screen">
    <div class="container mx-auto px-4 py-8 max-w-6xl">
        <!-- Header -->
        <div class="text-center mb-8">
            <h1 class="text-4xl font-bold text-gray-800 dark:text-white mb-2">📚 學測單字卡</h1>
            <p class="text-gray-600 dark:text-gray-400">點擊卡片查看例句</p>
        </div>
        <!-- Search Bar -->
        <div class="mb-8">
            <div class="relative max-w-2xl mx-auto">
                <input
                    type="text"
                    id="searchInput"
                    placeholder="搜尋單字或中文意思..."
                    class="w-full px-6 py-4 text-base rounded-full border-2 border-gray-300 dark:border-gray-600 focus:border-purple-500 dark:focus:border-purple-400 focus:outline-none bg-white dark:bg-gray-800 text-gray-800 dark:text-white shadow-lg transition-all"
                >
                <span class="absolute right-6 top-1/2 transform -translate-y-1/2 text-gray-400">🔍</span>
            </div>
            <div class="text-center mt-4">
                <span id="resultCount" class="text-gray-600 dark:text-gray-400"></span>
            </div>
        </div>
        <!-- Word Cards Grid -->
        <div id="cardsContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <!-- Cards will be generated here -->
        </div>
        <!-- No Results Message -->
        <div id="noResults" class="hidden text-center py-16">
            <p class="text-2xl text-gray-400 dark:text-gray-500">找不到相關單字</p>
        </div>
    </div>
    <script>
        // Dark mode detection
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.classList.add('dark');
        }
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', event => {
            if (event.matches) {
                document.documentElement.classList.add('dark');
            } else {
                document.documentElement.classList.remove('dark');
            }
        });
        // Vocabulary data (學測常考單字)
        const vocabulary = [
            {
                word: "abandon",
                chinese: "放棄；拋棄",
                example: "They had to abandon their home because of the flood.",
                translation: "因為洪水，他們不得不放棄他們的家。"
            },
            {
                word: "ability",
                chinese: "能力",
                example: "She has the ability to speak three languages fluently.",
                translation: "她有能���流利地說三種語言。"
            },
            {
                word: "absolute",
                chinese: "絕對的；完全的",
                example: "I have absolute confidence in your decision.",
                translation: "我對你的決定有絕對的信心。"
            },
            {
                word: "absorb",
                chinese: "吸收",
                example: "Plants absorb water through their roots.",
                translation: "植物透過根部吸收水分。"
            },
            {
                word: "academic",
                chinese: "學術的；學業的",
                example: "His academic performance has improved significantly.",
                translation: "他的學業表現有顯著的進步。"
            },
            {
                word: "accept",
                chinese: "接受",
                example: "I gladly accept your invitation to the party.",
                translation: "我很高興接受你的派對邀請。"
            },
            {
                word: "access",
                chinese: "接近；使用權",
                example: "Students have access to the library 24 hours a day.",
                translation: "學生可以24小時使用圖書館。"
            },
            {
                word: "accompany",
                chinese: "陪伴；伴隨",
                example: "Would you like me to accompany you to the doctor?",
                translation: "你想要我陪你去看醫生嗎？"
            },
            {
                word: "accomplish",
                chinese: "完成；達成",
                example: "We accomplished our mission ahead of schedule.",
                translation: "我們提前完成了我們的任務。"
            },
            {
                word: "accurate",
                chinese: "準確的；精確的",
                example: "The weather forecast was surprisingly accurate.",
                translation: "天氣預報出人意料地準確。"
            },
            {
                word: "achieve",
                chinese: "達到；實現",
                example: "She worked hard to achieve her goals.",
                translation: "她努力工作以實現她的目標。"
            },
            {
                word: "acquire",
                chinese: "獲得；學到",
                example: "Children acquire language naturally through exposure.",
                translation: "孩子們透過接觸自然地學會語言。"
            },
            {
                word: "adapt",
                chinese: "適應；改編",
                example: "Animals must adapt to their environment to survive.",
                translation: "動物必須適應環境才能生存。"
            },
            {
                word: "additional",
                chinese: "額外的；附加的",
                example: "We need additional time to complete the project.",
                translation: "我們需要額外的時間來完成這個專案。"
            },
            {
                word: "adequate",
                chinese: "足夠的；適當的",
                example: "Make sure you get adequate sleep before the exam.",
                translation: "確保你在考試前獲得充足的睡眠。"
            },
            {
                word: "adjust",
                chinese: "調整；適應",
                example: "It takes time to adjust to a new environment.",
                translation: "適應新環境需要時間。"
            },
            {
                word: "admire",
                chinese: "欽佩；讚美",
                example: "I admire her courage and determination.",
                translation: "我欽佩她的勇氣和決心。"
            },
            {
                word: "admit",
                chinese: "承認；准許進入",
                example: "He finally admitted that he made a mistake.",
                translation: "他最終承認他犯了一個錯誤。"
            },
            {
                word: "advance",
                chinese: "前進；進步",
                example: "Technology continues to advance at a rapid pace.",
                translation: "技術持續快速進步。"
            },
            {
                word: "advantage",
                chinese: "優勢；好處",
                example: "One advantage of living in the city is better public transportation.",
                translation: "住在城市的一個好處是有更好的大眾運輸。"
            },
            {
                word: "adventure",
                chinese: "冒險",
                example: "Traveling to new countries is always an adventure.",
                translation: "到新的國家旅行總是一場冒險。"
            },
            {
                word: "affect",
                chinese: "影響",
                example: "The weather can affect our mood significantly.",
                translation: "天氣會顯著影響我們的心情。"
            },
            {
                word: "afford",
                chinese: "負擔得起；提供",
                example: "We can't afford to buy a new car right now.",
                translation: "我們現在負擔不起買新車。"
            },
            {
                word: "agriculture",
                chinese: "農業",
                example: "Agriculture is the main industry in this region.",
                translation: "農業是這個地區的主要產業。"
            },
            {
                word: "alternative",
                chinese: "替代的；選擇",
                example: "We need to find an alternative solution to this problem.",
                translation: "我們需要找到這個問題的替代解決方案。"
            },
            {
                word: "amaze",
                chinese: "使驚奇",
                example: "The magician's tricks never fail to amaze the audience.",
                translation: "魔術師的把戲總是讓觀眾驚奇不已。"
            },
            {
                word: "ambition",
                chinese: "野心；抱負",
                example: "Her ambition is to become a successful entrepreneur.",
                translation: "她的抱負是成為一位成功的企業家。"
            },
            {
                word: "ancient",
                chinese: "古代的；古老的",
                example: "We visited ancient ruins during our trip to Greece.",
                translation: "我們在希臘之旅中參觀了古代遺跡。"
            },
            {
                word: "anxious",
                chinese: "焦慮的；渴望的",
                example: "She felt anxious about the upcoming exam.",
                translation: "她對即將到來的考試感到焦慮。"
            },
            {
                word: "apparent",
                chinese: "明顯的；顯而易見的",
                example: "It was apparent that he was not telling the truth.",
                translation: "很明顯他沒有說實話。"
            },
            {
                word: "appeal",
                chinese: "吸引力；呼籲",
                example: "The new design has great appeal to young consumers.",
                translation: "新設計對年輕消費者有很大的吸引力。"
            },
            {
                word: "appreciate",
                chinese: "感激；欣賞",
                example: "I really appreciate your help with this project.",
                translation: "我真的很感激你在這個專案上的幫助。"
            },
            {
                word: "approach",
                chinese: "接近；方法",
                example: "We need a different approach to solve this problem.",
                translation: "我們需要不同的方法來解決這個問題。"
            },
            {
                word: "appropriate",
                chinese: "適當的；恰當的",
                example: "Please wear appropriate clothing for the formal event.",
                translation: "請為正式活動穿著適當的服裝。"
            },
            {
                word: "approve",
                chinese: "批准；贊成",
                example: "The committee approved the new budget proposal.",
                translation: "委員會批准了新的預算提案。"
            },
            {
                word: "artificial",
                chinese: "人工的；人造的",
                example: "Artificial intelligence is changing many industries.",
                translation: "人工智慧正在改變許多產業。"
            },
            {
                word: "aspect",
                chinese: "方面；層面",
                example: "We need to consider every aspect of the problem.",
                translation: "我們需要考慮問題的每個方面。"
            },
            {
                word: "assist",
                chinese: "協助；幫助",
                example: "The teacher will assist you with your homework.",
                translation: "老師會協助你完成作業。"
            },
            {
                word: "assume",
                chinese: "假設；認為",
                example: "Don't assume that everyone knows what you mean.",
                translation: "不要假設每個人都知道你的意思。"
            },
            {
                word: "atmosphere",
                chinese: "大氣；氣氛",
                example: "The restaurant has a warm and friendly atmosphere.",
                translation: "這家餐廳有溫暖友好的氣氛。"
            },
            {
                word: "attach",
                chinese: "附上；貼上",
                example: "Please attach your resume to the application form.",
                translation: "請將你的履歷附在申請表上。"
            },
            {
                word: "attempt",
                chinese: "嘗試；企圖",
                example: "She made several attempts to contact him.",
                translation: "她多次嘗試聯繫他。"
            },
            {
                word: "attitude",
                chinese: "態度；看法",
                example: "A positive attitude can help you overcome difficulties.",
                translation: "積極的態度可以幫助你克服困難。"
            },
            {
                word: "attract",
                chinese: "吸引",
                example: "The bright colors attract butterflies to the garden.",
                translation: "鮮豔的顏色吸引蝴蝶來到花園。"
            },
            {
                word: "available",
                chinese: "可獲得的；有空的",
                example: "The manager is not available at the moment.",
                translation: "經理現在沒空。"
            },
            {
                word: "average",
                chinese: "平均的；普通的",
                example: "The average temperature in summer is 30 degrees.",
                translation: "夏天的平均溫度是30度。"
            },
            {
                word: "avoid",
                chinese: "避免",
                example: "Try to avoid eating too much sugar.",
                translation: "盡量避免吃太多糖。"
            },
            {
                word: "award",
                chinese: "���；獎品",
                example: "She received an award for her outstanding performance.",
                translation: "她因出色的表現獲得了獎項。"
            },
            {
                word: "aware",
                chinese: "意識到的；知道的",
                example: "Are you aware of the risks involved?",
                translation: "你知道其中涉及的風險嗎？"
            },
            {
                word: "balance",
                chinese: "平衡；餘額",
                example: "It's important to maintain a balance between work and life.",
                translation: "在工作和生活之間保持平衡很重要。"
            }
        ];
        let currentExpandedCard = null;
        // Render cards
        function renderCards(words) {
            const container = document.getElementById('cardsContainer');
            const noResults = document.getElementById('noResults');
            const resultCount = document.getElementById('resultCount');
            if (words.length === 0) {
                container.innerHTML = '';
                noResults.classList.remove('hidden');
                resultCount.textContent = '';
                return;
            }
            noResults.classList.add('hidden');
            resultCount.textContent = `找到 ${words.length} 個單字`;
            container.innerHTML = words.map((item, index) => `
                <div class="word-card bg-white dark:bg-gray-800 rounded-2xl shadow-lg p-6 cursor-pointer" data-index="${index}">
                    <div class="card-front">
                        <h2 class="text-2xl font-bold text-purple-600 dark:text-purple-400 mb-2">${item.word}</h2>
                        <p class="text-gray-700 dark:text-gray-300 text-lg">${item.chinese}</p>
                        <p class="text-sm text-gray-500 dark:text-gray-400 mt-4">點擊查看例句 →</p>
                    </div>
                    <div class="card-back hidden mt-4">
                        <div class="example-content border-t border-white/30 pt-4">
                            <p class="text-base italic mb-2 opacity-90">"${item.example}"</p>
                            <p class="text-base opacity-80">「${item.translation}」</p>
                        </div>
                    </div>
                </div>
            `).join('');
            // Add click event listeners
            document.querySelectorAll('.word-card').forEach(card => {
                card.addEventListener('click', () => toggleCard(card));
            });
        }
        // Toggle card expansion
        function toggleCard(card) {
            const cardBack = card.querySelector('.card-back');
            const isExpanded = card.classList.contains('expanded');
            // Collapse previously expanded card
            if (currentExpandedCard && currentExpandedCard !== card) {
                currentExpandedCard.classList.remove('expanded');
                currentExpandedCard.querySelector('.card-back').classList.add('hidden');
            }
            // Toggle current card
            if (isExpanded) {
                card.classList.remove('expanded');
                cardBack.classList.add('hidden');
                currentExpandedCard = null;
            } else {
                card.classList.add('expanded');
                cardBack.classList.remove('hidden');
                currentExpandedCard = card;
            }
        }
        // Search functionality
        const searchInput = document.getElementById('searchInput');
        searchInput.addEventListener('input', (e) => {
            const searchTerm = e.target.value.toLowerCase().trim();
            if (searchTerm === '') {
                renderCards(vocabulary);
                return;
            }
            const filtered = vocabulary.filter(item =>
                item.word.toLowerCase().includes(searchTerm) ||
                item.chinese.includes(searchTerm)
            );
            renderCards(filtered);
        });
        // Initial render
        renderCards(vocabulary);
    </script>
</body>
</html>
