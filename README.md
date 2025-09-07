<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Community Agreement Jeopardy</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Calm Classroom -->
    <!-- Application Structure Plan: The SPA is a single-purpose application focused on the Jeopardy game. The structure is a main content area that holds the game board. A modal window is used for displaying questions and answers, which is a standard and intuitive UI pattern. This version adds a fourth category for math intervention, making the application a cross-curricular review tool. -->
    <!-- Visualization & Content Choices: The core interactive element is the Jeopardy game board. Goal: Reinforce knowledge of class agreements and basic integer operations. Presentation: A CSS grid creates the clickable game board. Questions and answers are displayed in a modal pop-up. Interaction: Users click a value on the board, a modal with the question appears, and they can click a button to reveal the answer. Justification: This classic game format is highly intuitive and makes practicing math feel less like a drill. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #FDFBF8;
            color: #4A4A4A;
        }
        .modal-backdrop {
            background-color: rgba(0,0,0,0.5);
        }
        .jeopardy-card {
            transition: all 0.2s ease-in-out;
        }
        .jeopardy-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }
    </style>
</head>
<body class="antialiased">

    <div id="app" class="min-h-screen">
        <header class="bg-white/80 backdrop-blur-md shadow-sm">
            <div class="container mx-auto px-4 py-4 text-center">
                 <h1 class="text-3xl font-bold text-stone-700">Community Agreement Jeopardy</h1>
            </div>
        </header>

        <main class="container mx-auto p-4 md:p-8">
            
            <div id="jeopardy-page">
                 <section class="text-center mb-12">
                    <p class="max-w-3xl mx-auto text-stone-600 text-lg">Ready to test your knowledge? Select a category and a point value. Answer the question, then click to reveal the correct response. Good luck!</p>
                </section>

                <div id="jeopardy-board" class="grid grid-cols-4 gap-2 md:gap-4">
                </div>
            </div>

        </main>
    </div>

    <div id="jeopardy-modal" class="hidden fixed inset-0 z-30 flex items-center justify-center p-4">
        <div class="modal-backdrop fixed inset-0"></div>
        <div class="bg-white rounded-2xl shadow-2xl p-6 md:p-8 w-full max-w-2xl z-40 transform transition-all scale-95 opacity-0">
            <h3 id="modal-category" class="text-sm font-bold uppercase text-stone-500 mb-2">Category</h3>
            <p id="modal-question" class="text-2xl md:text-3xl font-bold text-stone-800 mb-6 min-h-[100px]"></p>
            <div id="modal-answer-container" class="hidden">
                <p class="text-xl md:text-2xl font-semibold text-amber-700 bg-amber-50 p-4 rounded-lg"><span class="font-normal text-stone-500">A:</span> <span id="modal-answer"></span></p>
            </div>
            <div class="mt-6 flex space-x-4">
                <button id="show-answer-btn" class="w-full bg-stone-700 text-white px-6 py-3 rounded-md font-semibold hover:bg-stone-800 transition">Show Answer</button>
                <button id="close-modal-btn" class="w-full bg-stone-200 text-stone-800 px-6 py-3 rounded-md font-semibold hover:bg-stone-300 transition">Close</button>
            </div>
        </div>
    </div>


    <script>
        const jeopardyData = {
            categories: ["What's the Rule?", "Teacher Promises", "Classroom Scenarios", "Your Vibe Math Check"],
            questions: [
                { category: 0, points: 100, q: "This agreement is about being on time with your gear, ready to learn.", a: "What is 'Show Up & Level Up'?" },
                { category: 0, points: 200, q: "This agreement means it's okay to not understand something and to ask for help.", a: "What is 'Normalize Being Lost'?" },
                { category: 0, points: 300, q: "This rule is for solving conflicts with people directly, not talking behind their backs.", a: "What is 'Handle Beef IRL, Not on the Group Chat'?" },
                { category: 0, points: 400, q: "This agreement is about taking ownership of your mistakes and learning from them.", a: "What is 'Embrace the L'?" },
                { category: 0, points: 500, q: "This agreement encourages working together and sharing success in group assignments.", a: "What is 'Let's Collab, Not Compete'?" },
                
                { category: 1, points: 100, q: "This promise is about being fair with grades and rules.", a: "What is 'I'll keep it 100 with you'?" },
                { category: 1, points: 200, q: "This promise means the teacher will listen to student ideas and let them lead.", a: "What is 'I'll respect your voice'?" },
                { category: 1, points: 300, q: "This promise is about giving students the tools they need to take control of their learning.", a: "What is 'I'll be your co-pilot'?" },
                { category: 1, points: 400, q: "The agreement where your teacher promises to give you a clean slate and a second chance every day.", a: "What is 'I'll Be a Safe Place'?" },
                { category: 1, points: 500, q: "A teacher who is fair, listens, and provides tools is embodying which set of agreements?", a: "What are the Teacher Promises?" },

                { category: 2, points: 100, q: "A classmate drops their books. You stop to help them pick everything up.", a: "What is 'We Got Each Other's Backs'?" },
                { category: 2, points: 200, q: "You don't understand the homework. Instead of guessing, you raise your hand and ask the teacher for help.", a: "What is 'Normalize Being Lost'?" },
                { category: 2, points: 300, q: "Your friend is talking while the teacher is giving instructions. You quietly remind them to pay attention.", a: "What is 'Listen Up, No Cap'?" },
                { category: 2, points: 400, q: "You accidentally leave a candy wrapper on your desk. At the end of class, you make sure to throw it away.", a: "What is 'Respect the Drip (and the Room)'?" },
                { category: 2, points: 500, q: "You get a low score on a quiz. You decide to study the material you got wrong for the next test instead of just being upset.", a: "What is 'Embrace the L'?" },

                { category: 3, points: 100, q: "A good vibe score is a positive number! What is 8 + 5?", a: "What is 13?" },
                { category: 3, points: 200, q: "Your vibe score is at 10, but it drops by 12. What is 10 - 12?", a: "What is -2?" },
                { category: 3, points: 300, q: "You start with a low vibe of -3, but then you get 10 good vibe points! What is -3 + 10?", a: "What is 7?" },
                { category: 3, points: 400, q: "Think of a number line. If you are at 5 and move 9 spaces to the left, where are you now? (5 - 9)", a: "What is -4?" },
                { category: 3, points: 500, q: "You start at -8 on a number line. You move 10 steps to the right. Where do you land? (-8 + 10)", a: "What is 2?" },
            ]
        };

        document.addEventListener('DOMContentLoaded', () => {
            function setupJeopardy() {
                const board = document.getElementById('jeopardy-board');
                const modal = document.getElementById('jeopardy-modal');
                const modalBackdrop = modal.querySelector('.modal-backdrop');
                const modalContent = modal.querySelector('.z-40');
                const modalCategory = document.getElementById('modal-category');
                const modalQuestion = document.getElementById('modal-question');
                const modalAnswerContainer = document.getElementById('modal-answer-container');
                const modalAnswer = document.getElementById('modal-answer');
                const showAnswerBtn = document.getElementById('show-answer-btn');
                const closeModalBtn = document.getElementById('close-modal-btn');
                
                jeopardyData.categories.forEach(cat => {
                    const header = document.createElement('div');
                    header.className = 'bg-stone-700 text-white text-center font-bold p-2 md:p-4 rounded-t-lg text-xs sm:text-base';
                    header.textContent = cat;
                    board.appendChild(header);
                });

                const pointValues = [100, 200, 300, 400, 500];
                pointValues.forEach(points => {
                    jeopardyData.categories.forEach((cat, catIndex) => {
                         const questionData = jeopardyData.questions.find(q => q.category === catIndex && q.points === points);
                         const card = document.createElement('button');
                         card.className = 'jeopardy-card bg-stone-100 text-stone-700 font-bold p-4 md:p-6 rounded-lg text-xl sm:text-3xl hover:bg-amber-100 disabled:opacity-50 disabled:cursor-not-allowed';
                         card.textContent = `$${points}`;
                         if (questionData) {
                            card.addEventListener('click', () => {
                                modalCategory.textContent = jeopardyData.categories[questionData.category];
                                modalQuestion.textContent = questionData.q;
                                modalAnswer.textContent = questionData.a;
                                modalAnswerContainer.classList.add('hidden');
                                showAnswerBtn.classList.remove('hidden');
                                
                                modal.classList.remove('hidden');
                                setTimeout(() => {
                                   modalContent.classList.remove('scale-95', 'opacity-0');
                                }, 10);
                                
                                card.disabled = true;
                                card.classList.add('bg-stone-300');
                                card.classList.remove('hover:bg-amber-100');
                            });
                         }
                         board.appendChild(card);
                    });
                });

                function closeModal() {
                    modalContent.classList.add('scale-95', 'opacity-0');
                    setTimeout(() => {
                        modal.classList.add('hidden');
                    }, 200);
                }

                showAnswerBtn.addEventListener('click', () => {
                    modalAnswerContainer.classList.remove('hidden');
                    showAnswerBtn.classList.add('hidden');
                });

                closeModalBtn.addEventListener('click', closeModal);
                modalBackdrop.addEventListener('click', closeModal);
            }
            
            setupJeopardy();
        });
    </script>
</body>
</html>

