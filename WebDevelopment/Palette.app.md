palette的app.tsx

这个代码需要用户点击导航栏的login按钮才能登录。



```javascript
import { useState, useEffect } from "react";
import { Routes, Route, Navigate, RouteProps } from "react-router-dom";
import HeaderEn from "./components/Layout/HeaderEn";
import FooterEn from "./components/Layout/FooterEn";
import HomeStarting from "./pages/home/HomeStarting";
import SolutionStarting from "./pages/solutions/SolutionsStarting";
import SolutionCreateStarting from "./pages/solutions-create/SolutionsCreateStarting";
import SolutionDetailsTabStarting from "./pages/solutions-details-tabs/SolutionsDetailsTabStarting";
import Login from "./components/Auth/Login";

import { useTranslation } from "react-i18next";
import { LoadingSpinner, useCasestudies, useSolutions, useUserProfile } from "./components";

import { Amplify, Auth, Hub } from "aws-amplify";
import { ProtectedRoute } from "./components/Route/ProtectedRoute";
import { LoggedOut } from "./components/Auth/LoggedOut";

import SolutionEditStarting from "./pages/solutions-edit/SolutionsEditStarting";
import UserPreferences from "./pages/user-preferences/UserPreferences";
import { configureAwsSdk } from "./common/utils";

import updatedAwsConfig from "./config/awsConfig";
import CasestudiesStarting from "./pages/casestudies/CasestudiesStarting";
import CasestudiesCreateStarting from "./pages/casestudies-create/CasestudiesCreateStarting";
import CasestudiesEditStarting from "./pages/casestudies-edit/CasestudiesEditStarting";
import CasestudiesDetailsStarting from "./pages/casestudies-details/CasestudiesDetailsStarting";
import AskPalette from "./pages/ask-palette";
import paletteConfig from "./config/aws-palette-config.json";
import MathQuiz from "./pages/math-quiz";
import NewslettersStarting from "./pages/newsletters/NewslettersStarting";

const DEBUG = paletteConfig.debugMode;

Amplify.configure(updatedAwsConfig);

/******************************************************************************
 * Here comes the App();
 *****************************************************************************/
const App = () => {
  const [user, setUser] = useState<any>(null);
  const { t, i18n } = useTranslation();
  const { uiLanguage, setUiLanguage } = useUserProfile();

  const languageChangeHandler = (language: string) => {
    setUiLanguage(language);
    // setContentLanguage(language);
    i18n.changeLanguage(language);
  };

  // 在组件加载时自动切换到 uiLanguage
  useEffect(() => {
    i18n.changeLanguage(uiLanguage);
  }, [uiLanguage]);

  useEffect(() => {
    Hub.listen("auth", ({ payload: { event, data } }) => {
      switch (event) {
        case "signIn":
        case "cognitoHostedUI":
          getUser().then((userData) => {
            DEBUG && console.log("%cuserData", "color: blue; font-weight: bold;", userData);
            setUser(userData);
            configureAwsSdk();
          });
          break;
        case "signOut":
          setUser(null);
          break;
        case "signIn_failure":
        case "cognitoHostedUI_failure":
          console.log("Sign in failure", data);
          break;
      }
    });

    getUser().then((userData) => {
      setUser(userData);
      configureAwsSdk();
    });
  }, []);

  function getUser() {
    return Auth.currentAuthenticatedUser()
      .then((userData) => userData)
      .catch(() => {
        console.log("Not signed in");
        // [Important Note] To enable auto sign in, we can add below line; But when user logged out, it will automatically login again.
        // This means you are forced to stay logged in.
        // Auth.federatedSignIn({customProvider: 'AmazonEmployee'});
      });
  }

  // signInHandler is to enable user login manually by clicking the "Login" button.
  const signInHandler = () => {
    Auth.federatedSignIn({ customProvider: "AmazonEmployee" })
      .then(() => {
        console.log("User is logging in with federation.");
      })
      .catch(() => console.log("Error catched when the user is logging in with federation."));
  };

  const signOutHandler = () => {
    Auth.signOut()
      .then(() => {
        console.log("User was logged out.");
      })
      .catch(() => console.log("Error catched when the user is logging out."));
  };

  /*+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*/
  return (
    <>
      <HeaderEn
        signInHandler={signInHandler}
        signOutHandler={signOutHandler}
        amazonLogin={user ? JSON.parse(user.attributes.identities)[0].userId : "Login"}
        languageChangeHandler={languageChangeHandler}
      />
      <Routes>
        <Route path="/store/*" element={<StoreAsset />} />
        <Route path="/img/*" element={<ImgAsset />} />
        <Route
          path="/"
          element={
            <ProtectedRoute>
              <HomeStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/preferences"
          element={
            <ProtectedRoute>
              <UserPreferences />
            </ProtectedRoute>
          }
        />
        <Route
          path="/solutions"
          element={
            <ProtectedRoute>
              <SolutionStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/solutions/create"
          element={
            <ProtectedRoute>
              <SolutionCreateStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/solutions/edit"
          element={
            <ProtectedRoute>
              <SolutionEditStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/solutions/details"
          element={
            <ProtectedRoute>
              <SolutionDetailsTabStarting />
            </ProtectedRoute>
          }
        />

        <Route
          path="/casestudies"
          element={
            <ProtectedRoute>
              <CasestudiesStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/casestudies/create"
          element={
            <ProtectedRoute>
              <CasestudiesCreateStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/casestudies/edit"
          element={
            <ProtectedRoute>
              <CasestudiesEditStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/casestudies/details"
          element={
            <ProtectedRoute>
              <CasestudiesDetailsStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/newsletters"
          element={
            <ProtectedRoute>
              <NewslettersStarting />
            </ProtectedRoute>
          }
        />
        <Route
          path="/ask-palette"
          element={
            <ProtectedRoute>
              <AskPalette />
            </ProtectedRoute>
          }
        />
        <Route
          path="/math-quiz"
          element={
            <ProtectedRoute>
              <MathQuiz />
            </ProtectedRoute>
          }
        />
        <Route
          path="/cloud-drive"
          element={
            <ProtectedRoute>
              <StoreAsset />
            </ProtectedRoute>
          }
        />
        <Route
          path="/casestudies"
          element={
            <ProtectedRoute>
              <CasestudiesStarting />
            </ProtectedRoute>
          }
        />

        <Route path="/login" element={<Login />} />
        <Route path="/loggedout" element={<LoggedOut />} />
        <Route path="*" element={<Navigate to="/" />} />
      </Routes>
      <FooterEn />
    </>
  );
};

export default App;

const StoreAsset: React.FC<RouteProps> = () => {
  return null;
};

const ImgAsset: React.FC<RouteProps> = () => {
  return null;
};

```



headerEn组件的代码是：

```javascript
import TopNavigation from "@cloudscape-design/components/top-navigation";
import { useNavigate } from "react-router-dom";
import { useTranslation } from "react-i18next";
import { useUserProfile } from "../Context/UserProfileContext";

export interface HeaderEnProps {
  signInHandler?: () => void;
  signOutHandler?: () => void;
  amazonLogin: string;
  languageChangeHandler: (language: string) => void;
}

/*****************************************************************************/
const HeaderEn = ({ signInHandler, signOutHandler, amazonLogin, languageChangeHandler }: HeaderEnProps) => {
  const { t } = useTranslation();
  const { uiLanguage } = useUserProfile();
  const navigate = useNavigate();

  const showLanguage = uiLanguage === "en" ? t("topBarHeader.English") : t("topBarHeader.Chinese");

  const changeLanguageHandler = (event: any) => {
    event.preventDefault();
    console.log(event.detail.id);
    languageChangeHandler(event.detail.id);
  };

  const loginClickHandler = (event: any) => {
    event.preventDefault();
    signInHandler && signInHandler(); // 避免signInHandler为undefined
  };

  const userItemClickHandler = (event: any) => {
    event.preventDefault();
    console.log(event.detail);
    switch (event.detail.id) {
      case "signout":
        signOutHandler && signOutHandler(); // 避免signOutHandler为undefined
        break;
      case "preferences":
        navigate(event.detail.href); // 在原来窗口中显示新页面
        break;
      default: // 这里不要使用navigate
      // 打开新窗口
        window.open(event.detail.href, "_blank");
        break;
    }
  };

  return (
    <TopNavigation
      identity={{
        href: "/",
        title: t("name") || "Palette",
        onFollow: (event) => {
          event.preventDefault();
          navigate("/");
        }, //very important in terms of improving performance of redirecting to "/" (xuhi@)
        logo: {
          src: "data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPHN2ZyB3aWR0aD0iNDNweCIgaGVpZ2h0PSIzMXB4IiB2aWV3Qm94PSIwIDAgNDMgMzEiIHZlcnNpb249IjEuMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KICAgIDxnIHN0cm9rZT0ibm9uZSIgc3Ryb2tlLXdpZHRoPSIxIiBmaWxsPSJub25lIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiPgogICAgICAgIDxyZWN0IGZpbGw9IiMyMzJmM2UiIHN0cm9rZT0iI2Q1ZGJkYiIgeD0iMC41IiB5PSIwLjUiIHdpZHRoPSI0MiIgaGVpZ2h0PSIzMCIgcng9IjIiPjwvcmVjdD4KICAgICAgICA8dGV4dCBmb250LWZhbWlseT0iQW1hem9uRW1iZXItUmVndWxhciwgQW1hem9uIEVtYmVyIiBmb250LXNpemU9IjEyIiBmaWxsPSIjRkZGRkZGIj4KICAgICAgICAgICAgPHRzcGFuIHg9IjkiIHk9IjE5Ij5Mb2dvPC90c3Bhbj4KICAgICAgICA8L3RleHQ+CiAgICA8L2c+Cjwvc3ZnPgo=",
          alt: "Palette",
        },
      }}
      utilities={[
        {
          type: "button",
          text: t("topBarHeader.TeamWiki") || "Team Wiki",
          href: "https://w.amazon.com/bin/view/AWS/GCRSolutionCenter/",
          external: true,
          externalIconAriaLabel: "(opens in a new tab)",
        },
        {
          type: "menu-dropdown",
          ariaLabel: "Settings",
          title: "Language",
          text: showLanguage,
          onItemClick: changeLanguageHandler,
          items: [
            {
              id: "en",
              text: t("topBarHeader.English") || "English",
            },
            {
              id: "cn",
              text: t("topBarHeader.Chinese") || "Chinese",
            },
          ],
        },
        amazonLogin === "Login"
          ? {
              type: "button",
              text: t("topBarHeader.Login") || "Login",
              iconName: "user-profile",
              onClick: loginClickHandler,
            }
          : {
              type: "menu-dropdown",
              text: amazonLogin,
              description: amazonLogin,
              iconName: "user-profile",
              onItemClick: userItemClickHandler,
              items: [
                { id: "preferences", text: t("topBarHeader.Preferences") || "Preferences", href: "/preferences", external: false },
                {
                  id: "links",
                  text: t("topBarHeader.Links") || "Links",
                  items: [
                    {
                      id: "gcr-solutions-library",
                      text: t("topBarHeader.gcrSolutionsLibrary") || "GCR Solutions Library",
                      href: "https://www.amazonaws.cn/en/solutions/?nc1=h_ls",
                      external: true,
                      externalIconAriaLabel: "(opens in new tab)",
                    },
                    {
                      id: "ww-solutions-library",
                      text: t("topBarHeader.wwSolutionsLibrary") || "WW Solutions Library",
                      href: "https://aws.amazon.com/solutions/",
                      external: true,
                      externalIconAriaLabel: "(opens in new tab)",
                    },
                    {
                      id: "ww-quick-starts",
                      text: t("topBarHeader.wwQuickStarts") || "AWS Partner Solutions",
                      href: "https://aws.amazon.com/quickstart/?quickstart-all.sort-by=item.additionalFields.sortDate&quickstart-all.sort-order=desc&solutions-all.sort-by=item.additionalFields.sortDate&solutions-all.sort-order=desc&awsf.filter-content-type=*all&awsf.filter-tech-category=*all&awsf.filter-industry=*all",
                      external: true,
                      externalIconAriaLabel: "(opens in new tab)",
                    },
                  ],
                },
                { id: "signout", text: t("topBarHeader.Signout") || "Sign out" },
              ],
            },
      ]}
      i18nStrings={{
        searchIconAriaLabel: "Search",
        searchDismissIconAriaLabel: "Close search",
        overflowMenuTriggerText: "More",
        overflowMenuTitleText: "All",
        overflowMenuBackIconAriaLabel: "Back",
        overflowMenuDismissIconAriaLabel: "Close menu",
      }}
    />
  );
};

export default HeaderEn;

```



