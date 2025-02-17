## Dynamic content in Drupal: Implementation and removal process

**Goal:** To allow product teams to feature dynamic content as needed for UAT testing and staged rollouts. Then to move content back into Drupal once a product team has completed UAT testing/staged rollout. This is an important step to take post-launch. Having content and CTA buttons accessible in Drupal allows the Sitewide Content team to make updates as needed without help from developers. It also helps to avoid potential issues with showing the wrong content to users, if feature toggles remain but are no longer monitored.

**How the Sitewide Content team works with product teams to use feature toggles for dynamic content for UAT testing and staged rollouts:**
1. The product team works with the Sitewide Content team to determine what content needs to be dynamic. The teams work together to finalize content.

2.	The product team creates a react widget with the dynamic content.

3.	The Sitewide Content team adds the react widget to the Drupal page, which will entail moving some content out of Drupal content blocks. Sitewide Content should ensure that the new page with react widget matches the mockup page that the product team has provided.

4.	The product team creates a feature toggle to control the display of content. </br>**Note:** The product team is responsible for monitoring the feature toggle.

5.	Once the product is fully launched to 100% of users, the Sitewide Content team removes the react widget and puts the content and CTA button back into regular rich text fields within Drupal. 

**Steps to ensuring content moves back into Drupal in a timely manner:**
1.	When adding react widget, Sitewide Content team creates ticket with estimated date for widget removal. Date provided by product team, based on full launch to 100% of users.

2.	One week prior to estimated full launch date, Sitewide Content team confirms date with product team and adjusts timing if needed.

3.	When product team confirms that product is launched to 100% of users, Sitewide Content team removes react widget from Drupal page and adds text and CTA button back into rich text field. Sitewide Content team editor reviews and approves changes.

4.	Product team reviews and confirms changes.

5.	Sitewide Content team publishes page.

6.	Sitewide Content team and product team validate that CTA button on live, updated page continues to point to correct place.
