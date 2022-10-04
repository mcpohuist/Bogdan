Як створювати паки в R
========================================================
author: Богдан Дюжин, Семенюк Орест
date: https://github.com/mcpohuist/r-pkg-intro
autosize: true

Для чого писати власний пакет?
========================================================

Насправді це доволі зручно, коли ви робите власні функції, і хочете їх об'єднати в один пакет і тому подібне. Також щоб автоматизувати певні робочі процеси, пришвидшити роботу за допомогою наперед визначених методів, функцій.

Структура пакету
========================================================

- R code (`/R`)
- Documentation (`/man`)
- Tests (`/tests`)
- Package metadata (`DESCRIPTION`)
- Namespace (`NAMESPACE`)

***

- Data (`/data`)
- Vignettes (`/vignettes`)
- Compiled code (`/src`)
- Installed files (`/inst`)
- Other components

========================================================

## Option 1
File --> New Project --> New Directory --> R Package

## Option 2

```r
#install.package("devtools")
devtools::create("~/Desktop/greetings")
```

*Вимоги до іменування пакетів: (1) лише літери, цифри та крапки; (2) має починатися з літери; (3) не може закінчуватися крапкою*

Написання функції
========================================================

```r
say_aloha <- function(name, print = TRUE) {

  message <- paste("Aloha,",
                   name,
                   emo::ji("palm_tree"),
                   emo::ji("sunny"),
                   emo::ji("ocean"))

  if (print) {
    cat(crayon::bgGreen(message))
  }

  invisible(message)
}
```

*Як завжди, не забувайте писати описові назви функцій, які не збігаються з існуючими функціями*


```r
install.packages("crayon")
devtools::install_github("hadley/emo")
```

Додайте перевірки до своєї функції
========================================================

These should be included _inside_ your function:


```r
stopifnot(is.logical(print))

if (!(is.character(name))) {
  stop("Name must be a non empty character. Input a name you want to say aloha to!")
}
```

Перевірити вхідні дані функції легко зробити, і це позбавляє від багатьох проблем з налагодженням, якщо (а) зупинити функцію раніше і (б) надати вам зрозуміле повідомлення про помилку.

Напишіть свою документацію
========================================================
Use Tools --> Roxygen Quick Reference as a template

Common sections:
- title
- `@description`
- `@param` - parameters/arguments
- `@examples` - examples of how to use your function
- `@export` - make this function available to package users (helper functions do not need this)
- `@import`/`@importFrom` - import packages/functions from other packages; used to generate the `NAMESPACE`

*Використовуйте виклики імпорту замість викликів `library` або `require`*

Задокоментуйте вашу функцію:
========================================================

```r
#' Say Aloha
#'
#' @description This function will say aloha to any inputted name.
#'
#' @param name (character) A name to say aloha to.
#' @param print (logical) Option to print your message. Defaults to \code{TRUE}
#'
#' @return (character) An aloha message
#'
#' @examples
#' # Say hello to a friend
#' friend <- "Irene"
#' say_aloha(friend)
#'
#' @importFrom crayon bgGreen
#' @importFrom emo ji
#'
#' @export
```


Задокоментуйте ваш пакет:
========================================================

Автоматично створює файли довідки (`/man`) і файл `NAMESPACE`:


```r
# delete any *.Rd or NAMESPACE files before running for the first time
devtools::document()
```

Перевірте ваш пакет
========================================================


```r
devtools::check()
```

```
checking package dependencies ... ERROR
Namespace dependencies not required: ‘crayon’ ‘emo’

See section ‘The DESCRIPTION file’ in the ‘Writing R Extensions’
manual.
```

Опис файлу
========================================================

```r
Package: greetings
Title: What the Package Does (one line, title case)
Version: 0.0.0.9000
Authors@R: person("First", "Last", email = "first.last@example.com", role = c("aut", "cre"))
Description: What the package does (one paragraph).
Depends: R (>= 3.3.0)
License: What license is it under?
Encoding: UTF-8
LazyData: true
RoxygenNote: 6.0.1
```

Відредагуйте опис файлу
========================================================

```r
Package: greetings
Title: Say Aloha to a Friend
Version: 0.1
Authors@R: c(
  person("Bogdan", "Orest", comment = "https://github.com/mcpohuist", role = c("aut")),
  person("Diuzhun", "Semenuk", email = "duzunbogdan@gmail.com", role = c("cre", "aut")))
Description: This package provides a pleasant way to say hello or goodbye to a friend.
Depends: R (>= 3.3.0)
License: CC0
Encoding: UTF-8
LazyData: true
RoxygenNote: 6.0.1
Imports: crayon, emo
Remotes: hadley/emo
Suggests: testthat
```

Провірте ваш пакет знову 
========================================================


```r
devtools::check()
```

Модульні тести
========================================================

Замість того, щоб перевіряти ваші функції ad-hoc, напишіть **модульні тести**, щоб формалізувати ці перевірки та переконатися, що ваша функція працює так, як ви думаєте!
- менше помилок
- краща структура коду
- прості перезапуски
- надійний код


```r
devtools::use_testthat()
```

Напишіть модульний тест
========================================================
Новий скрипт на R: `test_FUNCTION_NAME.R`


```r
context("say_aloha function")

test_that("function takes one input", {
  testthat::expect_error(say_aloha(c("Bogdan", "Orest")))
})
```

Під'єднання до GitHub
========================================================
1. Створіть *порожнє* місце GitHub з такою ж назвою

2. У терміналі (Інструменти --> Shell) налаштуйте ім’я користувача та електронну адресу, якщо ви цього ще не зробили:
```
git config --global user.name YOUR_NAME
git config --global user.email GITHUB_EMAIL
```

3. Зв’яжіться з GitHub і надішліть локальні файли у свій онлайн-репозиторій GitHub
```
git remote add origin https://github.com/USERNAME/PACKAGE_NAME.git
# make a commit
git push -u origin master
```

YOUR TURN
========================================================
Make sure your package passes all the checks and get it on GitHub!

Install your neighbor's package and take a look at their documentation.


```r
devtools::install_github("username/reponame")
# ?reponame::function_name
```

Advanced topics
========================================================

- [continuous integration with Travis](#travis)
- [data files](#data-files)
- [vignettes](#vignettes)

Travis - continuous integration
========================================================
id: travis

![](static/travis.gif)

***

Log in to [Travis](https://travis-ci.org/) with GitHub and use it to:

- perform basic checks on R packages
- re-build bookdown books ([example](https://github.com/isteves/two-bookdowns-example))
- run custom scripts ([example](https://github.com/espm-157/popdyn-template))

.travis.yml
========================================================


```r
devtools::use_travis()
```

```
# R for travis: see documentation at https://docs.travis-ci.com/user/languages/r

language: r
r:
  - release
  - devel
sudo: false
cache: packages
```

Add data files
========================================================
id: data-files

Can create data files (e.g. `View(iris)`)

```r
friends <- c("Mitchell", "Irene")
devtools::use_data(friends)
```

Document data files
========================================================
Create a new `R/data.R` file


```r
#' Friends list
#'
#' A list of friends I like to say aloha to.
#'
#' @format A vector with two strings
"friends"
```


```r
devtools::document()
```

Document package
========================================================
Create a new `R/package_name.R` file

```r
#' greetings
#'
#' @description A package that provides a pleasant way to say hello or goodbye to a friend.
#'
#' @section greetings functions:
#' say_aloha
#'
"_PACKAGE"
```


```r
devtools::document()
```

Install package
========================================================

```r
devtools::install()
```

Or click `Install and Restart`

Troubleshooting
========================================================
![](static/restart-r.jpg)

Vignettes
========================================================
id: vignettes

Vignettes are Rmds that come bundled with your package. Rather than writing about a single function, it's useful to describe how your functions can be used together.


```r
devtools::use_vignette("say-aloha-to-your-friends")
```

Viewing vignettes
========================================================

When you install a package `devtools::install_github()` and friends, the vignette-building process is skipped by default. To unskip and checkout your vignette, run:


```r
devtools::install(build_vignettes = TRUE)
# devtools::install_github(build_vignettes = TRUE)
browseVignettes("greetings")
```

*Read more about vignettes in the [vignettes chapter](http://r-pkgs.had.co.nz/vignettes.html) of the R Packages book*

Explore package
========================================================

```r
greetings::say_aloha(greetings::friends)
```

```
Aloha, Mitchell 🌴 ☀️ 🌊 Aloha, Irene 🌴 ☀️ 🌊
```


```r
.rs.restartR() #restart R to see documentation
?greetings::say_aloha
?greetings::friends
```
