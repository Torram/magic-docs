This component creates a _div_ element with a **_[loadingIndicator](../Components/shared/LoadingIndicator)_**.
```tsx
import LoadingIndicator from '../components/shared/LoadingIndicator';

export default function LoadingScreen({ isLoading }: { isLoading?: boolean }): JSX.Element {
    return (
        <div className='d-flex w-100 vh-100 flex-column align-items-center'>
            <LoadingIndicator isLoading={isLoading} />
        </div>
    );
}
```