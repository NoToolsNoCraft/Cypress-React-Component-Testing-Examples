
# Cypress React Logout Button component test

This project provides a React LogoutButton component integrated with Auth0 for user logout functionality. A Cypress test ensures the button renders and triggers logout correctly. The button redirects users to the application's origin upon logout.






## React LogoutButton Component



```bash
import { useAuth0 } from "@auth0/auth0-react";
import { Button } from "@radix-ui/themes";

const LogoutButton = () => {
  const { logout } = useAuth0();

  return (
    <Button
      color="gray"
      variant="soft"
      onClick={() =>
        logout({ logoutParams: { returnTo: window.location.origin } })
      }
    >
      Log Out
    </Button>
  );
};

export default LogoutButton;
```


## Cypress Component Test



```bash
import { mount } from "cypress/react";
import LogoutButton from "../../src/components/LogoutButton"; // adjust path
import { Auth0Context } from "@auth0/auth0-react";

describe("<LogoutButton />", () => {
  it("renders and calls logout on click", () => {
    const logoutSpy = cy.stub().as("logoutSpy");

    // provide a fake Auth0 context
    const auth0ContextValue = {
      isAuthenticated: true,
      logout: logoutSpy,
      loginWithRedirect: cy.stub(),
      user: { name: "Test User" },
    };

    mount(
      <Auth0Context.Provider value={auth0ContextValue as any}>
        <LogoutButton />
      </Auth0Context.Provider>
    );

    // assert button renders
    cy.contains("Log Out").should("exist");

    // click button
    cy.contains("Log Out").click();

    // assert logout called with expected params
    cy.get("@logoutSpy").should("have.been.calledWith", {
      logoutParams: { returnTo: window.location.origin },
    });
  });
});
```

![Screenshot of Logout Button Component](Screenshot%202025-09-27%20112145.png)

| Criteria | Justification |
| :--- | :--- |
| **Isolation** | The test successfully isolates the `LogoutButton` component by **mocking** its external dependency, the `Auth0Context`. This ensures the test only focuses on the button's behavior and doesn't rely on a real Auth0 setup. |
| **Mocking Quality** | It uses a **Cypress stub (`cy.stub().as("logoutSpy")`)** to replace the real `logout` function provided by `useAuth0`. This is the perfect approach for tracking calls to external functions. |
| **Coverage** | The test covers the two main responsibilities of the component: 1) **Rendering** the button (`cy.contains("Log Out").should("exist")`) and 2) **Calling the correct function** with the **correct arguments** upon clicking (`cy.get("@logoutSpy").should("have.been.calledWith", ...)`). |
| **Readability** | The test is clear, well-structured, and easy to understand. The use of `.as("logoutSpy")` and `cy.get("@logoutSpy")` makes the intent explicit. |
| **Clarity of Assertions** | The assertion that the `logout` function is called includes the **exact parameters** it should receive (`{ logoutParams: { returnTo: window.location.origin } }`), which is crucial for verifying the logic inside the `onClick` handler. |
