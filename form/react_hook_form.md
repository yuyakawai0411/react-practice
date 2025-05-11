# 目的
react-hook-formでフォームを作成できるようにする
# 手段
今まで使っていた`formik`との書き方の違いを知ることでreact-hook-formのつい書い方を知る。

| 項目 | react-hook-form | formik |
| --- | --- | --- |
| パラダイム | Reactのrefとhook中心 | 状態管理（useState）中心 |
| 制御コンポーネント | 非制御コンポーネント中心（パフォーマンス重視） | 制御コンポーネント（stateで全体管理） |
| パフォーマンス | 高い（再レンダリング少ない） | やや低め（入力ごとに再レンダリング）|
| 公式UIバインディング | registerでDOMにバインド | valueとonChangeを手動でバインド |
| 学習コスト | やや高め（APIに慣れる必要あり） | 比較的低め（Reactらしい書き方） |

# formikの書き方
formikは基本的に以下の3つのステップに分かれる
- 初期化
- 入力値を保持
- 入力値を送信

```typescript
// 初期化
const formik = useFormik<Todo>({
  initialValues: initialTodos,
  onSubmit: async (values) => {
    await handleSubmit(values);
  },
  validationSchema: validationSchema
});
// 入力値を保持
<Input
  onChange={(e)=>formik.setValues('todoReport', value)}
  value={formik.values.todoReport} 
/>
// 入力値を送信
<Button
  onClink={formik.submitForm}
/>
```
# react-hook-form
-  const methods = useForm<DealParam>({mode: 'onBlur', resolver: yupResolver(validationSchema), defaultValues: buildDealParam(useProperty)});
- const { register, watch, setValue, formState, getValues, handleSubmit } = useFormContext<DealUpdateParam>();

minimal start
- useFormとuseFormContextの違い
- registerを波及させる場合と、setValueを使う場合の実装の違い

```typescript
/** @jsxImportSource @emotion/react */
import { css } from "@emotion/react";
import { useForm } from "react-hook-form";

const styles = {
  App: css`
    font-family: sans-serif;
    text-align: center;
  `,
  Form: css`
    display: flex;
    gap: 1rem;
  `,
};

export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm({
    defaultValues: { firstName: "", lastName: "" },
  });

  console.log(watch());

  return (
    <div css={styles.App}>
      <form
        css={styles.Form}
        onSubmit={handleSubmit((data) => {
          console.log("submitDate", data);
        })}
      >
        <input
          {...register("firstName", {
            required: "This is Required!",
            maxLength: { value: 4, message: "limit 4" },
          })}
          placeholder="first name"
        />
        <input
          {...register("lastName", { required: "This is Required!" })}
          placeholder="last name"
        />
        <input type="submit" />
      </form>
      <p>{errors.firstName?.message}</p>
    </div>
  );
}
```