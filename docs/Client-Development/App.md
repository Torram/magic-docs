---
sidebar_position: 1
---

Main file of the client side of project that includes every needed component.

```tsx
import './App.css';
import AuthenticationGuard from './components/Auth/AuthenticationGuard';
import { QueryClient, QueryClientProvider } from 'react-query';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import DashboardScreen from './screens/Dashboard.screen';
import AuthenticationScreen from './screens/Authentication.screen';
import { RecoilRoot } from 'recoil';

const client = new QueryClient({
    defaultOptions: {
        queries: {
            staleTime: Infinity,
            notifyOnChangeProps: 'tracked'
        }
    }
});

function App(): JSX.Element {
    return (
        <RecoilRoot>
            <BrowserRouter>
                <QueryClientProvider client={client}>
                    <div className='App'>
                        <Switch>
                            <Route path={'/authentication'}>
                                <AuthenticationScreen />
                            </Route>
                            <AuthenticationGuard>
                                <DashboardScreen />
                            </AuthenticationGuard>
                        </Switch>
                    </div>
                </QueryClientProvider>
            </BrowserRouter>
        </RecoilRoot>
    );
}

export default App;
```