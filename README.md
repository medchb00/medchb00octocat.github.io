# medchb00octocat.github.io
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>インタラクティブWebアプリ「美と健康の悩み相談室」</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Calm Harmony (Warm neutrals: bg-stone-50, text-stone-800; Accent: muted orange/terracotta for highlights) -->
    <!-- Application Structure Plan: The application uses a thematic, card-based dashboard layout. A top navigation bar allows users to filter content by major themes (Diet, Body Issues, etc.). This non-linear structure is chosen over a simple e-book format to allow users to immediately access topics of personal interest, enhancing usability. Key interactions include a dynamic bar chart for comparing food nutrients and accordions for breaking down complex topics, which prevents information overload and encourages exploration. -->
    <!-- Visualization & Content Choices: Food Data -> Goal: Compare -> Viz: Interactive Bar Chart (Chart.js) -> Interaction: User selects food, chart updates -> Justification: More engaging and clearer than a static table. Back Pain Causes -> Goal: Organize -> Viz: HTML/CSS Accordion -> Interaction: Click to expand/collapse -> Justification: Breaks down complex info into digestible chunks. UV Protection Tips -> Goal: Inform -> Viz: Styled HTML List with Unicode icons -> Justification: Simple, actionable checklist format is most effective. CONFIRMED: NO SVG/Mermaid used. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Noto Sans JP', sans-serif;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 400px;
            }
        }
        .nav-button {
            transition: all 0.3s ease;
        }
        .nav-button.active {
            background-color: #f97316;
            color: white;
            box-shadow: 0 4px 14px 0 rgba(249, 115, 22, 0.39);
        }
        .content-section {
            display: none;
        }
        .content-section.active {
            display: block;
        }
        .accordion-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease-out, padding 0.5s ease-out;
            padding: 0 1.5rem;
        }
        .accordion-button.active + .accordion-content {
            max-height: 1000px;
            padding: 1.5rem;
        }
        .accordion-button .icon::before {
            content: '+';
            transition: transform 0.3s ease;
        }
        .accordion-button.active .icon::before {
            transform: rotate(45deg);
        }
    </style>
</head>
<body class="bg-stone-50 text-stone-800">

    <header class="bg-white shadow-md sticky top-0 z-50">
        <div class="container mx-auto px-4 py-4 flex justify-between items-center">
            <h1 class="text-xl md:text-2xl font-bold text-orange-600">美と健康の悩み相談室</h1>
            <div class="hidden md:block">
                <nav id="desktop-nav" class="flex items-center space-x-2">
                </nav>
            </div>
            <div class="md:hidden">
                <select id="mobile-nav" class="w-full rounded-md border-gray-300 shadow-sm focus:border-orange-500 focus:ring-orange-500">
                </select>
            </div>
        </div>
    </header>

    <main class="container mx-auto p-4 md:p-8">
        <div id="app-content">
        </div>
    </main>

    <footer class="bg-stone-800 text-white mt-12 py-6">
        <div class="container mx-auto text-center">
            <p>&copy; 2025 メディカルコンサルH&B. All Rights Reserved.</p>
            <p class="text-sm text-stone-400 mt-2">このアプリケーションは情報提供を目的としており、専門的な医学的アドバイスに代わるものではありません。</p>
        </div>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const appData = {
                categories: [
                    { id: 'diet', name: '食事とダイエット' },
                    { id: 'body', name: '体の不調' },
                    { id: 'mental', name: '心の健康' },
                    { id: 'beauty', name: '美容ケア' },
                    { id: 'womens', name: '女性の悩み' }
                ],
                content: {
                    diet: {
                        title: '健康な体づくりのための食事とダイエット',
                        intro: 'このセクションでは、従来の「バランスの良い食事」の考え方を見直し、美しい体づくりを目指すための新しい食事法を探ります。タンパク質の重要性や、太りやすい・太りにくい食材の具体的な見分け方、インタラクティブなグラフで食品の栄養バランスを比較する方法などを学びます。',
                        articles: [
                            {
                                id: 'diet-balance',
                                title: '「バランスの良い食事」の新しい常識',
                                content: `
                                    <p class="mb-4">多くの方が「バランスの良い食事」と聞くと、主食・主菜・副菜が揃った食事を思い浮かべるでしょう。しかし、ダイエットや美しい体づくりを目的とする場合、その常識を一度見直す必要があります。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">タンパク質中心の食事へ</h4>
                                    <p class="mb-4">重要なのは、炭水化物よりもタンパク質の摂取量を多くすることです。タンパク質と炭水化物の比率が「タンパク質 > 炭水化物」となる食事を心がければ、太りにくい体質へと導くことができます。卵や肉、魚、大豆製品などはタンパク質が豊富ですが、実はこれらの食材にも少量の炭水化物が含まれています。そのため、意識的にタンパク質を多く摂ることが大切です。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">注意すべき「太りやすい食材」</h4>
                                    <p class="mb-4">ケーキやドーナツ、清涼飲料水はもちろんですが、意外にも「果物」は糖質、特に「果糖」が多いため注意が必要です。果糖は肝臓で中性脂肪に変換されやすく、満腹感を得にくいため、食べ過ぎにつながりやすい性質があります。健康や美容に良いとされるスムージーやアサイーも、摂取量には気をつけましょう。お菓子の代わりとして少量楽しむ程度が理想です。</p>
                                    <div class="bg-orange-100 border-l-4 border-orange-500 text-orange-700 p-4 my-6 rounded-r-lg">
                                        <p class="font-bold">プロの視点</p>
                                        <p>糖分の過剰摂取は、血糖値の急上昇を招き、イライラや空腹感の原因となるだけでなく、肌荒れにも繋がります。美容と健康のためには、糖度の低いレモンなどを選ぶのがおすすめです。</p>
                                    </div>
                                `
                            },
                            {
                                id: 'diet-chart',
                                title: 'インタラクティブ栄養比較',
                                content: `
                                    <p class="mb-4 text-center">下のボタンから食品を選ぶと、タンパク質と炭水化物の含有量をグラフで比較できます。普段何気なく食べている食品の栄養バランスを実感してみてください。</p>
                                    <div class="flex flex-wrap justify-center gap-2 mb-4">
                                        <button class="food-btn bg-white hover:bg-orange-100 text-stone-700 font-semibold py-2 px-4 border border-stone-300 rounded-full shadow-sm" data-food="chicken_breast">鶏胸肉</button>
                                        <button class="food-btn bg-white hover:bg-orange-100 text-stone-700 font-semibold py-2 px-4 border border-stone-300 rounded-full shadow-sm" data-food="tuna_sushi">マグロ寿司</button>
                                        <button class="food-btn bg-white hover:bg-orange-100 text-stone-700 font-semibold py-2 px-4 border border-stone-300 rounded-full shadow-sm" data-food="donut">ドーナツ</button>
                                        <button class="food-btn bg-white hover:bg-orange-100 text-stone-700 font-semibold py-2 px-4 border border-stone-300 rounded-full shadow-sm" data-food="smoothie">スムージー</button>
                                    </div>
                                    <div class="chart-container">
                                        <canvas id="foodChart"></canvas>
                                    </div>
                                `
                            },
                             {
                                id: 'diet-good-food',
                                title: '食べても太りにくい食材の秘密',
                                content: `
                                    <p class="mb-4">「太らない」と言われる食材は多くありますが、どんなものでも食べ過ぎは禁物です。ここでは「脂肪として体に残りにくい」食材をご紹介します。</p>
                                    <ul class="list-disc list-inside mb-4 space-y-2">
                                        <li><span class="font-bold">野菜類（葉物、もやし、ブロッコリーなど）:</span> 水分と食物繊維が豊富で、お腹の調子を整えます。</li>
                                        <li><span class="font-bold">海藻類（わかめ、ひじきなど）:</span> カロリーが非常に低く、ミネラルが豊富です。</li>
                                        <li><span class="font-bold">こんにゃく・きのこ類:</span> 食物繊維が多く、満腹感を得やすいのが特徴です。</li>
                                        <li><span class="font-bold">魚類:</span> 脂肪燃焼を助けるEPAやDHAが含まれますが、調理法には注意が必要です。</li>
                                    </ul>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">栄養不足に注意</h4>
                                    <p class="mb-4">これらの食材だけを食べる極端なダイエットは、栄養不足を招き、かえって痩せにくい体質になる可能性があります。体が飢餓状態と判断し、栄養を溜め込もうとするためです。健康的な体づくりには、十分なタンパク質を摂取し、基礎代謝を上げることが不可欠です。体重1kgあたり1g〜1.5gのタンパク質を目安に、1食20〜30gを数回に分けて摂るのがおすすめです。</p>
                                `
                            }
                        ]
                    },
                    body: {
                        title: '慢性的な体の不調との向き合い方',
                        intro: '頭痛、腰痛、風邪など、多くの人が経験する体の不調。このセクションでは、それぞれの症状の裏に隠された原因を専門家の視点から解説します。自己判断で済ませず、いつ病院へ行くべきか、そして医師とどう向き合うべきかなど、具体的な対処法を学び、つらい症状を改善する第一歩を踏み出しましょう。',
                        articles: [
                            {
                                id: 'body-headache',
                                title: '慢性的な頭痛の原因と対処法',
                                content: `
                                    <p class="mb-4">「いつものことだから」と頭痛を我慢していませんか？頭痛には種類があり、原因に応じた対処が重要です。自己判断せず、まずは原因を知るために専門医に相談しましょう。</p>
                                    <div class="space-y-4">
                                        <div class="bg-white p-4 rounded-lg shadow">
                                            <h4 class="font-bold text-orange-600">頭痛の種類と原因</h4>
                                            <p>一般的な頭痛には、筋肉の緊張からくる「筋緊張性頭痛」や、何らかのきっかけで起こる「片頭痛」があります。効果のある薬が異なるため、問診で原因を特定することが治療の第一歩です。最近では、効果の高い新しい注射薬も登場しています。</p>
                                        </div>
                                        <div class="bg-white p-4 rounded-lg shadow">
                                            <h4 class="font-bold text-orange-600">薬に頼らない予防法</h4>
                                            <p>意外かもしれませんが、運動、特に軽い筋力トレーニングやストレッチは頭痛予防に効果的です。運動によって「幸せホルモン」セロトニンが分泌され、心身のリフレッシュに繋がります。</p>
                                        </div>
                                        <div class="bg-red-100 border-l-4 border-red-500 text-red-700 p-4 my-6 rounded-r-lg">
                                            <p class="font-bold">要注意！危険な頭痛のサイン</p>
                                            <p>「いつもと違う、突然の激しい頭痛」は、くも膜下出血や脳腫瘍などのサインかもしれません。見え方がおかしい、鼻が腫れてきたなどの症状を伴う場合も危険です。すぐに医療機関を受診してください。</p>
                                        </div>
                                    </div>
                                `
                            },
                            {
                                id: 'body-backpain',
                                title: '長引く腰痛の意外な原因',
                                content: `
                                    <p class="mb-4">多くの人を悩ませる慢性的な腰痛。その原因は一つではありません。ここでは、考えられる主な原因と、検査で異常が見つからない場合の可能性について解説します。</p>
                                    <div id="backpain-accordion" class="space-y-2">
                                        <div>
                                            <button class="accordion-button w-full text-left font-semibold p-4 bg-white rounded-lg shadow flex justify-between items-center">
                                                <span>1. 姿勢のゆがみ</span>
                                                <span class="icon text-orange-600 text-2xl font-light"></span>
                                            </button>
                                            <div class="accordion-content bg-white rounded-b-lg shadow-inner">
                                                <p>長時間の前かがみ姿勢などは、背骨（椎骨）の一部だけがすり減る原因となり、腰が曲がってしまうことがあります。一度変形すると元に戻すのは困難です。</p>
                                            </div>
                                        </div>
                                        <div>
                                            <button class="accordion-button w-full text-left font-semibold p-4 bg-white rounded-lg shadow flex justify-between items-center">
                                                <span>2. 骨密度の低下</span>
                                                <span class="icon text-orange-600 text-2xl font-light"></span>
                                            </button>
                                            <div class="accordion-content bg-white rounded-b-lg shadow-inner">
                                                <p>特に50代以降の女性は、閉経による女性ホルモンの減少で骨密度が低下し、椎骨がもろくなることがあります。これにより椎骨が潰れ、痛みの原因となります。</p>
                                            </div>
                                        </div>
                                        <div>
                                            <button class="accordion-button w-full text-left font-semibold p-4 bg-white rounded-lg shadow flex justify-between items-center">
                                                <span>3. 椎間板ヘルニア</span>
                                                <span class="icon text-orange-600 text-2xl font-light"></span>
                                            </button>
                                            <div class="accordion-content bg-white rounded-b-lg shadow-inner">
                                                <p>椎骨の間にあるクッション（椎間板）がはみ出し、神経を圧迫して痛みを引き起こします。多くは自然に改善しますが、重度の場合は手術も選択肢となります。</p>
                                            </div>
                                        </div>
                                        <div>
                                            <button class="accordion-button w-full text-left font-semibold p-4 bg-white rounded-lg shadow flex justify-between items-center">
                                                <span>4. ストレス性腰痛</span>
                                                <span class="icon text-orange-600 text-2xl font-light"></span>
                                            </button>
                                            <div class="accordion-content bg-white rounded-b-lg shadow-inner">
                                                <p>レントゲンやCTで異常がないにも関わらず痛みが続く場合、ストレスが原因の可能性があります。この場合、神経の痛みに作用する特定の薬が効果的なことがあります。医師に「異常なし」と言われても諦めず、症状を伝え、別の可能性について相談することが大切です。</p>
                                            </div>
                                        </div>
                                    </div>
                                `
                            },
                             {
                                id: 'body-doctor',
                                title: '病院との上手な付き合い方',
                                content: `
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">風邪をひいたら？市販薬の限界</h4>
                                    <p class="mb-4">市販の風邪薬は、熱や咳などの症状を一時的に「緩和」するもので、原因のウイルスや細菌を退治するものではありません。また、眠くなる成分が含まれているため、だるさが続くこともあります。<span class="font-bold">38℃以上の熱が2日以上、咳が4〜5日以上続く、または1週間経っても症状が改善しない場合</span>は、医療機関を受診しましょう。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">症状が改善しない…どうすればいい？</h4>
                                    <p class="mb-4">一度病院で「問題ない」と言われたのに症状が続く場合、多くの人は別の病院を探しがちです。しかし、最適な選択は<span class="font-bold">「もう一度同じ病院へ行くこと」</span>です。</p>
                                    <p class="mb-4">医師は初診時に複数の可能性を考えています。再受診し「前回の薬では改善しなかった」と伝えることで、医師は別の治療法を検討したり、より専門的な病院への紹介状を書いてくれたりします。これにより、初診が無駄にならず、治療が前進します。諦めずに、同じ医師に相談してみましょう。</p>
                                `
                            }
                        ]
                    },
                    mental: {
                        title: '心の不調と健康',
                        intro: 'ストレス社会と呼ばれる現代。心が疲れると、食欲がなくなったり、やる気が出なくなったりと、体にも影響が現れます。このセクションでは、ストレスが心身に与える影響のメカニズムを解き明かし、ストレスと上手に付き合うための具体的な考え方やヒントを提供します。',
                        articles: [
                            {
                                id: 'mental-stress-appetite',
                                title: 'ストレスで食べられなくなるのはなぜ？',
                                content: `
                                    <p class="mb-4">強いストレスを感じると食欲がなくなるのは、自律神経の乱れが胃腸の機能に直接影響を与えるためです。ストレスは胃酸の過剰分泌を促す一方で、胃粘膜を守る血流を悪化させます。これにより、胃が自らの胃酸で傷つきやすくなり、胃痛や不快感から「食べたくない」と感じるようになるのです。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">どう対処すればいい？</h4>
                                    <p class="mb-4">胃の調子が良い時に、おかゆやスープなど消化しやすく温かいものを少量から試してみましょう。過剰な胃酸を中和し、胃への負担を和らげることができます。しかし、痛みがひどい場合は無理せず医療機関を受診してください。</p>
                                    <div class="bg-orange-100 border-l-4 border-orange-500 text-orange-700 p-4 my-6 rounded-r-lg">
                                        <p class="font-bold">「痩せ＝美」の危険な考え方</p>
                                        <p>ストレスで食べられないことを「痩せられてラッキー」と考えるのは非常に危険です。栄養不足は肌や髪の質を悪化させるだけでなく、将来の健康に大きな影響を及ぼします。20代の栄養状態が、その人の一生の健康を左右すると言っても過言ではありません。</p>
                                    </div>
                                `
                            },
                            {
                                id: 'mental-stress-management',
                                title: '日常のストレスを上手に受け流す方法',
                                content: `
                                    <p class="mb-4">ストレスを完全になくすことは不可能です。大切なのは、ストレスをどう受け止めるかです。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">「軽減しよう」と考えない</h4>
                                    <p class="mb-4">「このストレスが嫌だ」と強く思えば思うほど、そのことにとらわれ、症状は悪化しがちです。ストレスを感じたときに、「ストレスは誰でも感じるもの」と軽く受け流す意識を持つことが、最も効果的な対処法です。</p>
                                    <p class="mb-4">ストレスに支配されるのではなく、客観的に捉える練習をしましょう。どうしても自分で対処しきれない場合は、カウンセリングや薬の力を借りることもためらわないでください。大切なのは、一人で抱え込まないことです。</p>
                                `
                            }
                        ]
                    },
                    beauty: {
                        title: '美しい肌とまつげを手に入れるための知識',
                        intro: '日々のスキンケアから専門的な美容施術まで、美しさを追求するための知識は尽きません。このセクションでは、シミやニキビ、花粉による肌荒れといった日常的な悩みへの対処法から、化粧品の効果的な使い方、まつげ美容のリスク管理まで、プロの視点から徹底解説します。',
                        articles: [
                            {
                                id: 'beauty-sunscreen',
                                title: 'シミを作らないための紫外線対策',
                                content: `
                                    <p class="mb-4">将来の肌のために、紫外線対策は一年中欠かせません。特に夏場は、以下の3つのポイントを徹底しましょう。</p>
                                    <ol class="list-decimal list-inside space-y-3 mb-4">
                                        <li><span class="font-bold">日焼け止めを徹底的に塗る:</span> 室内でも窓際など紫外線は届きます。外出しない日でもSPF30以上の日焼け止めを顔と首に塗りましょう。外出時は2〜3時間ごとの塗り直しが理想です。汗をかいたらその都度塗り直すと効果が持続します。</li>
                                        <li><span class="font-bold">物理的に日差しを防ぐ:</span> 日傘、帽子、サングラスは必須アイテムです。目から入る紫外線も日焼けの原因になるため、サングラスは特に重要です。</li>
                                        <li><span class="font-bold">夏こそ保湿を意識する:</span> 夏は肌がベタつきがちですが、保湿を怠ると肌のバリア機能が低下します。シミは紫外線だけでなく、摩擦などのダメージでも発生します。しっかり保湿してバリア機能を高め、汗を拭く際はゴシゴシこすらず、押さえるように拭きましょう。</li>
                                    </ol>
                                `
                            },
                            {
                                id: 'beauty-acne',
                                title: '「たかがニキビ」と侮らないで！',
                                content: `
                                    <p class="mb-4">ニキビが一つできただけでも、皮膚科や美容皮膚科を受診することは全く問題ありません。自己判断で市販薬を使っても改善しない場合は、専門医に相談するのが改善への近道です。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">ニキビを悪化させないスキンケア</h4>
                                    <ul class="list-disc list-inside mb-4 space-y-2">
                                        <li><span class="font-bold">「ノンコメドジェニック」を選ぶ:</span> ニキビの原因菌のエサになりにくく、毛穴に詰まりづらい成分で作られた化粧品を使いましょう。</li>
                                        <li><span class="font-bold">オイル美容とグリセリンを避ける:</span> オイル系の化粧品はアクネ菌のエサになりやすく、高濃度のグリセリンもニキビを悪化させる可能性があります。</li>
                                        <li><span class="font-bold">低刺激の日焼け止めを使う:</span> ニキビ跡がシミになるのを防ぐため、紫外線対策は必須です。赤ちゃんでも使えるような低刺激の日焼け止めを選びましょう。</li>
                                    </ul>
                                `
                            },
                            {
                                id: 'beauty-cosmetics',
                                title: '化粧品、いつまで使える？',
                                content: `
                                    <p class="mb-4">肌に直接触れるものだからこそ、化粧品の使用期限は重要です。雑菌の繁殖は肌荒れの大きな原因になります。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">使用期限の目安（開封後）</h4>
                                    <p class="mb-2">あくまでも清潔に使用し、冷暗所で保管した場合の目安です。</p>
                                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                                        <div class="bg-white p-4 rounded-lg shadow">
                                            <h5 class="font-bold">スキンケア用品</h5>
                                            <ul class="list-disc list-inside">
                                                <li>化粧水・乳液・クリーム: 半年～1年</li>
                                                <li>オーガニック系: 3ヶ月以内</li>
                                            </ul>
                                            <p class="text-sm mt-2 text-red-600">推奨: 1〜2ヶ月で使い切るのが理想</p>
                                        </div>
                                        <div class="bg-white p-4 rounded-lg shadow">
                                            <h5 class="font-bold">コスメ用品</h5>
                                            <ul class="list-disc list-inside">
                                                <li>マスカラ・アイライナー: 3ヶ月～半年</li>
                                                <li>リキッドファンデ・リップ: 半年～1年</li>
                                                <li>パウダー製品: 1年～2年</li>
                                            </ul>
                                            <p class="text-sm mt-2 text-red-600">推奨: 3ヶ月程度で新品に</p>
                                        </div>
                                    </div>
                                    <p class="font-bold">分離、変な匂い、質感の変化は劣化のサインです。すぐに使用を中止しましょう。</p>
                                `
                            },
                            {
                                id: 'beauty-eyelash',
                                title: 'まつげ美容のリスク管理',
                                content: `
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">まつげエクステ・パーマのリスク</h4>
                                    <p class="mb-4">手軽に目元の印象を変えられますが、リスクも伴います。エクステは接着剤によるアレルギー、パーマは薬剤によるダメージが主なリスクです。信頼できるサロンで事前にパッチテストを行い、自まつげに負担の少ないデザインや頻度（エクステは3週間〜1ヶ月、パーマは1ヶ月半〜2ヶ月が目安）を守ることが大切です。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">海外製まつげ美容液の注意点</h4>
                                    <p class="mb-4">一部の海外製まつげ美容液には、元々緑内障の治療薬として使われていた成分（ビマトプロスト等）が含まれていることがあります。これらは育毛効果が高い一方で、色素沈着などの副作用のリスクがあります。日本では「グラッシュビスタ」という名称で医師の処方が必要です。安易にネット通販などで購入せず、安全性を重視するなら国内メーカーの保湿・美容成分が主体の製品を選ぶか、クリニックで処方してもらうのが安心です。</p>
                                `
                            }
                        ]
                    },
                    womens: {
                        title: '女性特有の悩みと向き合う',
                        intro: '月経に関する悩みは、多くの女性が抱えるデリケートな問題です。このセクションでは、PMS（月経前症候群）の緩和などに用いられる低用量ピルについて、その仕組みや効果、副作用を正しく理解し、不安を解消することを目指します。',
                        articles: [
                            {
                                id: 'womens-pill',
                                title: '低用量ピルは本当に安全？',
                                content: `
                                    <p class="mb-4">PMSや月経中のつらい症状を緩和するため、低用量ピルを服用する人が増えています。ピルは「排卵を止める」ホルモン剤です。女性ホルモン（エストロゲンとプロゲステロン）を少量投与し続けることで、脳を「常に排卵後」の状態だと認識させ、自然な排卵を抑制します。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">なぜPMSに効くの？</h4>
                                    <p class="mb-4">PMSの不調は、排卵後にプロゲステロンが大量に分泌されることが一因です。ピルを服用すると、ホルモンの分泌量や変動が穏やかになるため、つらい症状が緩和されるのです。</p>
                                    <h4 class="text-lg font-bold text-orange-600 mb-2">副作用は？</h4>
                                    <p class="mb-4">かつては体重増加などの副作用がありましたが、現在の低用量・超低用量ピルでは稀です。体質によってむくみなどが出る場合もありますが、薬の種類を変えることで改善することがあります。</p>
                                    <div class="bg-orange-100 border-l-4 border-orange-500 text-orange-700 p-4 my-6 rounded-r-lg">
                                        <p class="font-bold">最も大切なこと</p>
                                        <p>ピルの服用は、自己判断で開始・中止せず、必ず医師の指示に従ってください。不安な点があれば、納得できるまで産婦人科医に相談することが、安全な服用への第一歩です。</p>
                                    </div>
                                `
                            }
                        ]
                    }
                }
            };

            const foodData = {
                chicken_breast: { label: '鶏胸肉', protein: 24.4, carbs: 0 },
                tuna_sushi: { label: 'マグロ寿司', protein: 13.5, carbs: 18.8 },
                donut: { label: 'ドーナツ', protein: 4.5, carbs: 56.5 },
                smoothie: { label: 'スムージー', protein: 2.4, carbs: 11.7 }
            };

            let foodChart;

            const desktopNav = document.getElementById('desktop-nav');
            const mobileNav = document.getElementById('mobile-nav');
            const appContent = document.getElementById('app-content');

            function renderContent(categoryId) {
                const category = appData.content[categoryId];
                if (!category) return;

                let html = `<div id="${categoryId}" class="content-section active">
                    <h2 class="text-3xl font-bold mb-2 text-stone-700">${category.title}</h2>
                    <p class="text-lg text-stone-600 mb-8">${category.intro}</p>
                    <div class="space-y-12">`;

                category.articles.forEach(article => {
                    html += `
                        <article id="${article.id}" class="bg-white p-6 rounded-xl shadow-lg">
                            <h3 class="text-2xl font-bold mb-4 text-orange-600">${article.title}</h3>
                            <div class="prose max-w-none text-stone-700">${article.content}</div>
                        </article>
                    `;
                });

                html += `</div></div>`;
                appContent.innerHTML = html;

                if (categoryId === 'diet') {
                    initFoodChart('chicken_breast');
                    addFoodButtonListeners();
                }
                if (categoryId === 'body') {
                    addAccordionListeners();
                }
            }

            function updateNav(activeId) {
                const desktopButtons = document.querySelectorAll('#desktop-nav .nav-button');
                desktopButtons.forEach(button => {
                    button.classList.toggle('active', button.dataset.id === activeId);
                });
                mobileNav.value = activeId;
            }

            function handleNavClick(e) {
                const categoryId = e.target.dataset.id;
                if (categoryId) {
                    renderContent(categoryId);
                    updateNav(categoryId);
                }
            }

            function handleMobileNavChange(e) {
                const categoryId = e.target.value;
                if (categoryId) {
                    renderContent(categoryId);
                    updateNav(categoryId);
                }
            }

            appData.categories.forEach(cat => {
                const button = document.createElement('button');
                button.className = 'nav-button text-sm font-medium py-2 px-4 rounded-full bg-white border border-stone-200 text-stone-600 hover:bg-orange-500 hover:text-white hover:shadow-lg';
                button.textContent = cat.name;
                button.dataset.id = cat.id;
                desktopNav.appendChild(button);

                const option = document.createElement('option');
                option.value = cat.id;
                option.textContent = cat.name;
                mobileNav.appendChild(option);
            });

            desktopNav.addEventListener('click', handleNavClick);
            mobileNav.addEventListener('change', handleMobileNavChange);

            function initFoodChart(initialFood) {
                const ctx = document.getElementById('foodChart')?.getContext('2d');
                if (!ctx) return;

                const data = foodData[initialFood];
                foodChart = new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: ['タンパク質', '炭水化物'],
                        datasets: [{
                            label: `${data.label} (g/100g)`,
                            data: [data.protein, data.carbs],
                            backgroundColor: [
                                'rgba(234, 88, 12, 0.6)',
                                'rgba(120, 113, 108, 0.6)'
                            ],
                            borderColor: [
                                'rgba(234, 88, 12, 1)',
                                'rgba(120, 113, 108, 1)'
                            ],
                            borderWidth: 1
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        indexAxis: 'y',
                        plugins: {
                            legend: {
                                display: true
                            },
                            tooltip: {
                                callbacks: {
                                    label: function(context) {
                                        return `${context.dataset.label}: ${context.raw} g`;
                                    }
                                }
                            }
                        },
                        scales: {
                            x: {
                                beginAtZero: true,
                                title: {
                                    display: true,
                                    text: 'グラム (g / 100gあたり)'
                                }
                            }
                        }
                    }
                });
            }

            function updateFoodChart(foodId) {
                if (!foodChart || !foodData[foodId]) return;
                const data = foodData[foodId];
                foodChart.data.datasets[0].label = `${data.label} (g/100g)`;
                foodChart.data.datasets[0].data = [data.protein, data.carbs];
                foodChart.update();
            }

            function addFoodButtonListeners() {
                const foodButtons = document.querySelectorAll('.food-btn');
                foodButtons.forEach(button => {
                    button.addEventListener('click', () => {
                        updateFoodChart(button.dataset.food);
                    });
                });
            }

            function addAccordionListeners() {
                const accordionContainer = document.getElementById('backpain-accordion');
                if(accordionContainer) {
                    accordionContainer.addEventListener('click', function(e) {
                        const button = e.target.closest('.accordion-button');
                        if (button) {
                            button.classList.toggle('active');
                        }
                    });
                }
            }

            renderContent('diet');
            updateNav('diet');
        });
    </script>
</body>
</html>
