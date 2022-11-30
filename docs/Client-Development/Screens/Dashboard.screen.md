Component that creates a container page for system settings.

```tsx
import { useUser } from '../hooks/Auth/useUser';
import { tokenErrors } from '../tokens/tokenErrors';
import NavBar from '../components/shared/NavBar';
import { Redirect, Route, Switch } from 'react-router-dom';
import SetsScreen from './Sets.screen';
import ErrorIndicator from '../components/shared/ErrorIndicator';
import BoxScreen from './Box.screen';
import InventoryLogsScreen from './InventoryLogs.screen';
import ShopifyLogsScreen from './ShopifyLogs.screen';
import { SettingsScreen } from './Settings.screen';
import ListsScreen from './Lists.screen';
import ListScreen from './List.screen';
import LoadingScreen from './Loading.screen';
import ContentCenterDiv from '../components/shared/ContentCenterDiv';

export default function DashboardScreen(): JSX.Element {
    const { error, data, isLoading, isError } = useUser();

    if (isLoading) {
        return <LoadingScreen isLoading={isLoading} />;
    } else if (isError) {
        if (error instanceof Error && error.message === tokenErrors.noRefreshToken) {
            return <Redirect to={'/authentication'} />;
        } else {
            return (
                <ContentCenterDiv>
                    <ErrorIndicator isError={isError} error={error} />
                </ContentCenterDiv>
            );
        }
    } else {
        return (
            <>
                <NavBar user={data} />
                <Switch>
                    <Route path='/dashboard/boxes' exact component={SetsScreen} />
                    <Route path='/dashboard/boxes/:boxID' exact component={BoxScreen} />
                    <Route path='/dashboard/lists' exact component={ListsScreen} />
                    <Route path='/dashboard/lists/:listID' exact component={ListScreen} />
                    <Route path='/dashboard/inventory_logs' exact component={InventoryLogsScreen} />
                    <Route path='/dashboard/shopify_logs' exact component={ShopifyLogsScreen} />
                    <Route path='/dashboard/settings' exact component={SettingsScreen} />
                </Switch>
            </>
        );
    }
}
```