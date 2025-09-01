### Writing Sample: User Guide Excerpt 

* **Topic:** Assigning Orders to Staff Using Rule Profiles
* **Audience:** Platform users who process and assign orders to external vendors.
* **Goal:** To explain how to use "mixed configurations" within a rule profile to automate order assignment for some clients while allowing manual assignment for others.

# Rule Profile Settings: Mixed Configurations

An effective order assignment process often requires a blend of automation and manual control. By using a mixed configuration, you can set up a single **Admin Rule Profile** to automatically assign orders from some clients while allowing staff to manually assign orders from others.

This flexibility is achieved by combining two key settings: **Round Robin** and **Monitor Only**.

## Understanding the Settings

### Round Robin

This is an automated assignment method. When an order matches a parameter set to Round Robin, the system automatically assigns the order to an available user on a rotational basis. This is ideal for scenarios where speed is a top priority.

### Monitor Only

This setting puts control in the hands of your team. When an order matches a parameter set to Monitor Only, the system does not automatically assign it. Instead, all assigned users can view the order on their dashboards and decide who is best suited to take it on.

## How to Create a Mixed Configuration

To use both settings within the same rule profile, ensure the **Round Robin** checkbox is selected for the profile. This will display a toggle control next to each individual parameter within the configuration table.

By default, this toggle will read `[round robin]`. To change a specific parameter to Monitor Only, simply click the toggle. The text will update to `[monitor only]`, indicating that orders matching this parameter will now require manual assignment.

## Example:

Imagine a company that handles orders for two different clients: Client A and Client B.

**Client A** has a high volume of orders that need to be assigned as quickly as possible. For this client, the rule profile's Client parameter is set to `[round robin]`. Any order from Client A will be automatically assigned.

**Client B** is a smaller client with less frequent orders. For this client, the rule profile's Client parameter is set to `[monitor only]`. This allows staff to view the order and manually assign it to a team member with a lighter workload, ensuring that Client B's needs are handled with a personal touch.

By using a mixed configuration, the company can maintain an efficient, automated workflow for high-volume clients while still providing careful, manual oversight for others. This approach ensures that all orders are assigned in the most effective way possible, based on specific client needs and business priorities.

