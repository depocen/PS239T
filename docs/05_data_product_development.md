# Developing data products {#products}



> A data product is the production output from a statistical analysis. - [Brian Caffo](https://sites.google.com/view/bcaffo/home)

## Developing R packages 

1. Reuse your code 
2. Automate your workflow 
3. Help others (be part of an open source development community)

### Workflow 

1. Write code in `\R`
2. Document code in `\man` (automated by `roxygen2` package)
  - `devtools::document()` 
3. Check dependencies in `NAMESPACE`
  - `devtools::update()` updates the documentation 
  - `devtools::check()` to see whether your package is ready to be submitted to CRAN
4. Build a package (for more information, read [this section](http://r-pkgs.had.co.nz/package.html) in Hadley's R package development book)
  - `devtools::build()` 
5. (Optional) Test (`devtools::test()`), teach in `\vignettes`, and add data in `\data`
6. Distribute the package either via CRAN or GitHub  

![](http://r-pkgs.had.co.nz/diagrams/package-files.png)

It's time to learn five states of R code: source, bundled, binary, installed, and in-memory. 

If you're just using an R package, you're only concerned of the last two states: `install.packages("pkg")` and `library(pkg)` If you're developing an R package, you first write source code (`*.R`), bundle it (compressed file like `*.tar.gz`; done by `devtools::build()`), then make it binary (`devtools::build(binary = TRUE)`; This is how a package is stored in CRAN/GitHub, etc.).

### Required Components

The 4 required components are necessary to build and distribute a minimally viable R package. The other steps are optional.

- Package 
  - `\R`: R functions 
  - `\man`: function documentations 
  - DESCRIPTION: provides meta data about the package (e.g., author)
  - LICENSE
    - GNU, MIT, etc.
  - NAMESPACE: package dependencies (to make your package self-contained)
  - README (optional)
  
1. Setup (**DESCRIPTION**)


```r
# This function creates DESCRIPTION file 
usethis::create_package(here("mypkg"))

# Initialize git repo 
usethis::use_git()

# License the package 
# You can use the MIT license by typing devtools::use_mit_license("author name"). The function produces MIT license related files (LICENSE, LICENSE.md).
use_mit_license("Jae Yeon Kim")

# Add README (optional)
# Makes the package more use-friendly 
use_readme_md()

# Add news (optional) 
# Helps track changes 
use_news_md() 
```

2. Write code (**R**)


```r
usethis::use_r("rbind_mutate")
```


```r
#' Add two numbers
#'
#' @param x A number
#' @param y A number
#' @return The sum of x and y 
#' @export

add <- function(x, y){
  
  x + y
  
}
```

If you used a function from other packages you need to reference it in the following way: `#' @importFrom <package> <function>`

3. Document (**man**)


```r
# Document 
# The function creates documentation related files (NAMESPACE, function_name.rd)
devtools::document()

# Check; updates the documentation; builds and checks the package 
devtools::check()
```

4. Organize (**NAMESPACE**)


```r
usethis::use_package("dplyr")
```

### Optional Components  

1. Test (**test**)


```r
usethis::use_testthat()

usethis::use_test("rbind_mutate")
```

2. Add data (**data**)


```r
x <- "Jae"
y <- "Sun"
z <- "Jane"

usethis::use_data(x, y, z, overwrite = TRUE)
```

3. Teach (**vignetts**)


```r
usethis::use_vignette("rbind_mutate")
```

```r
title: "Vignette title"
author: "Vignette author"
date: "2021-03-01"
output: rmarkdown::html_vignette
vignette: blah blah
``` 

- You can build a package website using `pkgdown`


```r
# install.packages("pkgdown")
usethis::use_pkgdown()
pkgdown::build_site()
```

- A package site includes information on METADATA, Function references, Articles, News, etc. 

### Building an R package 

- CMD (in the terminal)

You can run R commands in the terminal using R CMD.



- devtools 


```r
# Build 
devtools::build()

# Install 
devtools::install()
```

### Distributing an R package 


```r
# Version update 
usethis::use_version()

# Spell check
usethis::use_spell_check()
```

1. [CRAN (The Comprehensive R Archive Network)](https://cran.r-project.org/)
  - R package submission should comply with [the CRAN Repository Policy](https://cran.r-project.org/)

2. GitHub 
  - Push everything to the Git repository (you can do it using command-line interface or RStudio).



  - Don't forget that your repository should be `public`.
  
  - I highly recommend connecting GitHub with SSH. For more information, visit [this link](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh).
  


**Additional tips**

1. If you want to use pipe operator (%) in your functions, save the following script as `utils-pipe.R.` 


```r
#' Pipe operator
#'
#' See \code{magrittr::\link[magrittr:pipe]{\%>\%}} for details.
#'
#' @name %>%
#' @rdname pipe
#' @keywords internal
#' @export
#' @importFrom magrittr %>%
#' @usage lhs \%>\% rhs
NULL
```

```
## NULL
```

2. Sometimes, you get the following error: "Undefined global functions or variables" If you experience this problem, save the following script as `globals.r.`


```r
utils::globalVariables(c("<undefined variable name1>", "<undefined variable name2>", "<undefinedvariable name3"))
```

```
## [1] "<undefined variable name1>" "<undefined variable name2>"
## [3] "<undefinedvariable name3"
```

## Developing Shiny apps

[Shiny](https://shiny.rstudio.com/) is a "framework for creating web applications using R code". You can create a dashboard or an interactive map without knowing anything about HTML, CSS, or JavaScript. Developing a shiny app helps people with little technical expertise to learn from your data in an intuitive and interactive way.

```{=html}

<iframe width="560" height="315" src="https://www.youtube.com/embed/Wy3TY0gOmJw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p>Shiny in production: Principles, practices, and tools - Joe Cheng</p>

```

```{=html}

<iframe src="https://vac-lshtm.shinyapps.io/ncov_tracker/?_ga=2.240702211.1091983227.1603295793-100003412.1602392815" style="border: 1px solid #AAA; width: 800px; height: 700px"></iframe>

<p>COVID-19 tracker by Edward Parker</p>

```

### Workflow 

The workflow follows what Hadley Wickham recommended in his book on mastering shiny. 

1. Install libraries 

```r
install.packages("shiny")
```

2. Create app directory and file 

Add an `app.R` file.

The key objective here is defining your UI (how the app looks; front-end = INPUT) (defined in object `ui`) and server (how the app works; back-end = OUTPUT) (defined in object `server`).

If you're creating a complex app, you can achieve the same goal with two files: `ui.R` and `server.R`.

### app.r 

- Front-end


```r
# Load packages 
# Do not use install.packages(), pacman::p_load(), or library() if you intend to deploy the app using shinyapps.io 

require("wordcloud2")
require("shiny")
require("shinydashboard")
require("colourpicker")

# Load data 

df <- read.csv(url("https://github.com/jaeyk/covid19antiasian/raw/master/processed_data/hash_counts.csv"))[,-1]

# Defines the user interface; how the app looks

ui <- fluidPage(
  
    # Application title 
    titlePanel("Word Cloud on the Hashtags of the Tweets related to COVID-19 & Asian|Chinese|Wuhan"),
  
    h4(tags$a(href = "https://jaeyk.github.io/", "Developer: Jae Yeon Kim")),
            
    sidebarLayout(
      
      # Sidebar with sliders 
      sidebarPanel(
        sliderInput("size", 
                    "Font size:",
                    min = 1, max = 10,
                    value = 2)
      ),
    
    mainPanel(
          
          wordcloud2Output("cloud"),
        
        )
    
    )
)
```

- Back-end


```r
server <- function(input, output, session) {
  
  output$cloud <- renderWordcloud2({ 
    
    wordcloud2(df, 
               size = input$size, 
               color = "random-dark") 
    
    })

  }
```

- Build a shiny app 


```r
shinyApp(ui = ui, server = server)
```

### Deployment 

- Deploy to [the shinyapps.io cloud](https://www.shinyapps.io/?_ga=2.5503866.871102833.1602978469-100003412.1602392815) 


```r
# Install packages 
install.packages("rsconnect")
library(rsconnect)

# Setup 
rsconnect::setAccountInfo(name = "<Account name>", 
                          token = "<Token>",
                          secret = "<Secret>")

rsconnect::deployApp(appNames = "<App name>")
```

### References 

[Mastering Shiny](https://mastering-shiny.org/) by Hadley Wickham. For newbies. 

[Shiny Documents](https://bookdown.org/yihui/rmarkdown/shiny-documents.html) by Yihui Xie

[Engineering Production-Grade Shiny Apps](https://engineering-shiny.org/) by Colin Fay, Sébastien Rochette, Vincent Guyader, Cervan Girard. For experienced developers.

[Building Shiny Apps](https://stat545.com/shiny-tutorial.html) by Dean Attali.

## Other useful data products 

- Automating data reports using rmarkdown (called [parameterized reports](https://rmarkdown.rstudio.com/developer_parameterized_reports.html%23parameter_types%2F))
- Automating R presentation using [slidify](http://slidify.org/index.html)
- Creating interactive web apps using [leaflet](https://rstudio.github.io/leaflet/) 
