# Playwright Java Accelerator

A robust, maintainable, and modern test automation framework using Java 17 and Playwright. This framework utilizes TestNG for test orchestration, Allure for rich reporting, SLF4j/Logback for logging, and includes best practices like the Page Object Model, parallel execution readiness, and automatic screenshots on failure.

## Features

- **Modern Language**: Built with Java 17
- **Powerful Browser Automation**: Microsoft Playwright for Java
- **Robust Test Orchestration**: TestNG for parallel execution, grouping, and more
- **Rich Reporting**: Allure Reports with screenshots, steps, and test details
- **Effective Logging**: SLF4j with Logback implementation
- **Maintainable Design Patterns**: Page Object Model and fluent interfaces
- **Configuration Management**: YAML-based configuration with environment-specific profiles
- **Streamlined Assertions**: AssertJ for fluent, powerful assertions
- **Clean Code**: Optional use of Lombok to reduce boilerplate code

## Project Structure

```
src/
  main/
    java/
      io/
        github/
          mypixelquest/
            pja/                      # Main source code (if applicable)
  test/
    java/
      io/
        github/
          mypixelquest/
            pja/
              base/                   # Base test classes & Playwright setup
                BaseTest.java         # Core test setup & teardown
              config/                 # Configuration models
                ConfigModel.java      # YAML configuration POJO
              listeners/              # TestNG & Allure listeners
                ScreenshotListener.java  # Auto-screenshot on failure
              pages/                  # Page Object Model classes
                BasePage.java         # Base page object functionality
                ExamplePage.java      # Example page implementation
              tests/                  # TestNG test classes
                ExampleTest.java      # Example test implementation
              utils/                  # Helper utilities
                ConfigReader.java     # Configuration loader
    resources/
      config/                         # Configuration files
        qa.yaml                       # QA environment config
      logback.xml                     # Logging configuration
      suites/                         # TestNG XML suite files
        example-suite.xml             # Example-specific test suite
        testng.xml                    # Main test suite
```

## Prerequisites

- Java 17 or higher
- Maven 3.6 or higher

## Getting Started

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/mypixelquest/playwright-java-accelerator.git
   cd playwright-java-accelerator
   ```

2. Install browser binaries for Playwright:
   ```bash
   mvn exec:java -e -D exec.mainClass=com.microsoft.playwright.CLI -D exec.args="install"
   ```

### Running Tests

Run the default test suite:
```bash
mvn test
```

Run specific test suites:
```bash
mvn test -DsuiteXmlFile=src/test/resources/suites/example-suite.xml
mvn test -DsuiteXmlFile=src/test/resources/suites/testng.xml
```

Run with specific browser:
```bash
mvn test -Dbrowser=firefox
```

Run in headed mode:
```bash
mvn test -Dheadless=false
```

Run tests with specific environment:
```bash
mvn test -Denvironment=qa
```

Run a specific test group:
```bash
mvn test -Dgroups=smoke
```

### Generating Reports

Generate and open Allure report:
```bash
mvn allure:report
mvn allure:serve
```

## Configuration

The framework uses YAML configuration files located in `src/test/resources/config/` as the single source of truth for all configuration settings.

Example configuration (qa.yaml):
```yaml
environment:
  name: qa
  baseUrl: https://playwright.dev/

browser:
  type: chromium  # chromium, firefox, or webkit
  headless: true
  slowMo: 0       # milliseconds to wait between actions
  timeout: 30000  # default timeout in milliseconds
  
screenshot:
  takeOnFailure: true
  fullPage: true

testExecution:
  parallel: true     # enable/disable parallel execution
  threadCount: 3     # number of parallel threads to use
```

### Configuration Management

The framework uses a configuration management system that:
- Centralizes all settings in YAML configuration files
- Supports environment-specific configurations
- Allows runtime overrides via system properties
- Uses TestNG listeners for dynamic configuration

Common configuration overrides:
```bash
# Override browser settings
mvn test -Dbrowser.type=firefox -Dbrowser.headless=true

# Override parallel execution
mvn test -DtestExecution.parallel=false
mvn test -DtestExecution.threadCount=4

# Override environment
mvn test -Denvironment=qa
```

### Parallel Execution

The framework supports dynamic parallel execution configuration through the YAML config file. Parallel execution can be:
- Enabled/disabled via YAML configuration
- Controlled at runtime via system properties
- Configured for number of parallel threads
- Applied at the method level for maximum parallelization

To modify parallel execution settings:
1. Via YAML (qa.yaml):
   ```yaml
   testExecution:
     parallel: true
     threadCount: 3
   ```

2. Via command line:
   ```bash
   mvn test -DtestExecution.parallel=true -DtestExecution.threadCount=4
   ```

## Included Test Examples

The framework includes examples demonstrating how to test the Playwright.dev website:

### Playwright Website Tests

The `ExampleTest` class demonstrates various interactions with the Playwright documentation website:

- **Homepage Navigation Test**: Verifies basic navigation and "Get Started" functionality
- **Java Documentation Test**: Tests navigation to Java-specific documentation
- **Search Functionality Test**: Validates the search feature and modal dialog
- **Tools Navigation Test**: Tests navigation between different Playwright tools (Codegen, Trace Viewer)

These tests showcase:
- Page Object Model implementation
- Playwright's auto-waiting capabilities
- Handling different types of UI elements
- Navigation between different sections
- Modal dialog interaction
- URL verification
- Element visibility checks

Run these tests with:
```bash
mvn test
```

Or specifically:
```bash
mvn test -DsuiteXmlFile=src/test/resources/suites/example-suite.xml
```

## Creating Tests

### 1. Create a Page Object

```java
public class ExamplePage extends BasePage {
    // Example implementation showing Playwright locators and methods
    private final Locator getStartedButton;
    private final Locator searchButton;
    private final Locator javaLink;
    
    public ExamplePage(Page page) {
        super(page);
        this.getStartedButton = page.locator("a.getStarted_Sjon");
        this.searchButton = page.locator("button.DocSearch");
        this.javaLink = page.locator("a[href='/java/']");
    }
    
    @Step("Navigate to homepage")
    public ExamplePage navigate() {
        page.navigate("https://playwright.dev");
        return this;
    }
    
    @Step("Click Get Started button")
    public ExamplePage clickGetStarted() {
        getStartedButton.click();
        return this;
    }
    
    @Step("Open search dialog")
    public ExamplePage openSearch() {
        searchButton.click();
        return this;
    }
}
```

### 2. Create a Test Class

```java
@Epic("Playwright Website Tests")
@Feature("Basic Website Navigation")
public class ExampleTest extends BaseTest {
    @Test(description = "Verify homepage navigation")
    @Severity(SeverityLevel.BLOCKER)
    public void testHomePageNavigation() {
        getCurrentPage().ifPresent(page -> {
            ExamplePage examplePage = new ExamplePage(page);
            examplePage.navigate()
                      .clickGetStarted();
                      
            Assertions.assertThat(examplePage.getCurrentUrl())
                     .contains("docs/intro");
        });
    }
}
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
