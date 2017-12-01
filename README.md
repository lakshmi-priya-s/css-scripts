/// <reference path="typings/jquery/jquery.d.ts" />
/// <reference path="typings/toastr/toastr.d.ts" />


let quizContainer = <HTMLElement>document.getElementById('quiz');
let resultsContainer = <HTMLElement>document.getElementById('results');
let submitButton = <HTMLElement>document.getElementById('submit');


let myQuestions:any[] = [
    {
        question: "Who is the strongest?",
        answers:
        {
            a: "Superman",
            b: "The Terminator",
            c: "Waluigi, obviously"
        },
        correctAnswer: "c"
    },
    {
        question: "What is the best site ever created?",
        answers:
        {
            a: "SitePoint",
            b: "Simple Steps Code",
            c: "Trick question; they're both the best"
        },
        correctAnswer: "c"
    },
    {
        question: "Where is Waldo really?",
        answers:
        {
            a: "Antarctica",
            b: "Exploring the Pacific Ocean",
            c: "Sitting in a tree",
            d: "Minding his own business, so stop asking"
        },
        correctAnswer: "d"
    }
];

buildQuiz();

//submitButton.addEventListener('click', showResults);

function buildQuiz(): void {
    $.ajax({
        type: 'Get',
        url: 'GetQuestions',
        contentType: 'application/json; charset=utf-8',
        dataType: 'json',
        success: function (data) {
            // Do something interesting here.
            console.log(data);
            myQuestions = data;
            buildData()
        },
        error: function (error) {
            console.log(error);
        }
    });
    // we'll need a place to store the HTML output
    
}

function buildData(): void {
    let output: any[] = [];

    // for each question...
    myQuestions.forEach(
        (currentQuestion, questionNumber) => {

            // we'll want to store the list of answer choices
            const answers = [];

            // and for each available answer...
            for (let letter in currentQuestion.answers) {

                //const l = letter;
                // ...add an HTML radio button
                if (currentQuestion.answers[letter] != null) {
                    answers.push(
                        `<div class="form-group">
                    <label class="radio-inline">
                    <input type="radio" name="question${questionNumber + 1}" value="${letter}"/>
                    ${currentQuestion.answers[letter]}
                    </label>
                    </div>`
                    );
                }
            }

            // add this question and its answers to the output
            output.push(
                `<div class="well-sm alert alert-info" id="q${questionNumber + 1}">
                <label class="question"> Q${questionNumber+1}) ${currentQuestion.question} </label>
                <div class="answers"> ${answers.join('')} </div>
                </div>`
            );
        }
    );

    // finally combine our output list into one string of HTML and put it on the page
    quizContainer.innerHTML = output.join('');
}

function invokeSubmit(): void{

    if (ValidationForEveryQuestionAnswered()) {
    
        // gather answer containers from our quiz
        let answerContainers = quizContainer.querySelectorAll('.answers');

        // keep track of user's answers
        let numCorrect: number = 0;

        // for each question...
        myQuestions.forEach((currentQuestion, questionNumber) => {

            // find selected answer
            const answerContainer = answerContainers[questionNumber];
            const selector = 'input[name=question' + (questionNumber+1) + ']:checked';
            const userAnswer = (<HTMLInputElement>(answerContainer.querySelector(selector) || {})).value;
            console.log(userAnswer + "-" + currentQuestion.correctAnswer)
            // if answer is correct
            if (userAnswer === currentQuestion.correctAnswer) {
                // add to the number of correct answers
                numCorrect++;

                // color the answers green
                jQuery('#q' + (questionNumber + 1)).addClass("alert-danger")
                answerContainers[questionNumber];
            }
            // if answer is wrong or blank
            else {
                // color the answers red
                //answerContainers[questionNumber].style.color = 'red';
            }
        });

        // show number of correct answers out of total
        resultsContainer.innerHTML = numCorrect + ' out of ' + myQuestions.length;
    }
    else {
        //do some logic for validations fail.
        myQuestions.forEach((currentQuestion, questionNumber) => {
            if (!jQuery("input[name=question" + (questionNumber + 1) + "]").is(":checked")) {
                jQuery('#q' + (questionNumber + 1)).removeClass("alert-info");
                jQuery('#q' + (questionNumber + 1)).addClass("alert-danger");
            }
            else {
                jQuery('#q' + (questionNumber + 1)).removeClass("alert-danger");
                jQuery('#q' + (questionNumber + 1)).addClass("alert-info");
            }
        });
        //toastr.options.positionClass = 'toast-top-middel'
        toastr.error("Please answer all questions...!", "Error")
    }
}

function ValidationForEveryQuestionAnswered(): boolean {

    let NoOfQusAnswered: number = 0;
    myQuestions.forEach((currentQuestion, questionNumber) => {
        if (jQuery("input[name=question" + (questionNumber + 1) + "]").is(":checked")) {
            NoOfQusAnswered++;
        }
        jQuery('#q' + (questionNumber + 1)).removeClass("alert-danger");
        jQuery('#q' + (questionNumber + 1)).addClass("alert-info");
    //jQuery("input[name=question]").each(function () {
    //    if (jQuery(this).is(":checked")) {
            
    //    }

    })
    console.log(NoOfQusAnswered + "-" + myQuestions.length)
    return (NoOfQusAnswered === myQuestions.length);
}






@{
    ViewBag.Title = "Security Awareness Module-1";
}

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SAQ</title>
    <link href="~/Content/toastr.css" rel="stylesheet" />
    <script src="~/Scripts/saq.js"></script>
</head>
<h2>Security Awareness Module-1</h2>
<br />
<div class="container">

    <div class="row">

        <div class="col-lg-12 col-md-12 col-sm-12 col-xs-12">
            <div class="pre-scrollable" id="quiz">
                </div>
            </div>
        </div>
    </div>
<br><br>
<div class="form-group">
    <input type="button" class="btn btn-success" value="Submit" onclick="invokeSubmit()" />
</div>
<div id="results"></div>
<script src="~/Scripts/toastr.js"></script>
<script src="~/Scripts/TsSaq.js"></script>
