import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;

public class FlipkartTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        // Set up WebDriver (Chrome in this case)
        System.setProperty("webdriver.chrome.driver", "/path/to/chromedriver"); // Set the path to your chromedriver
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        
        // Open Flipkart website
        driver.get("https://www.flipkart.com");
        
        // Close login popup if it appears
        try {
            WebElement closePopup = driver.findElement(By.xpath("//button[contains(text(),'✕')]"));
            closePopup.click();
        } catch (Exception e) {
            System.out.println("Login popup did not appear.");
        }
    }

    @Test
    public void searchAndAddToCartTest() throws InterruptedException {
        // Search for a product (e.g., "iPhone")
        WebElement searchBox = driver.findElement(By.name("q"));
        searchBox.sendKeys("iPhone");
        searchBox.submit();
        
        // Wait for the results to load
        Thread.sleep(3000);
        
        // Assert search results page loaded
        String pageTitle = driver.getTitle();
        Assert.assertTrue(pageTitle.contains("iPhone"), "Search results not loaded correctly");

        // Select the first item from the list
        WebElement firstItem = driver.findElement(By.xpath("//div[@class='_1AtVbE']/div[2]//a"));
        firstItem.click();

        // Switch to the new tab opened by the product page
        for (String winHandle : driver.getWindowHandles()) {
            driver.switchTo().window(winHandle);
        }
        
        // Click on "Add to Cart" button
        WebElement addToCartButton = driver.findElement(By.xpath("//button[text()='ADD TO CART']"));
        addToCartButton.click();
        
        // Wait for the item to be added to the cart
        Thread.sleep(2000);
        
        // Assert the item is added to the cart
        WebElement cartMessage = driver.findElement(By.xpath("//span[contains(text(),'My Cart')]"));
        Assert.assertTrue(cartMessage.isDisplayed(), "Item was not added to the cart.");
    }

    @AfterMethod
    public void tearDown() {
        // Close the browser after the test
        driver.quit();
    }
}

