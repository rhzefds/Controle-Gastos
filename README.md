# Monitoramento Financeiro

Este projeto começa como uma dashboard local em HTML/JS e pode ser conectado a um banco online com Supabase.

## Estrutura de arquivos

- `index.html` — dashboard principal com gráficos e KPIs
- `monitoramento.html` — versão base de acompanhamento de gastos
- `supabase-config.example.js` — modelo de configuração para Supabase

## Como usar localmente

1. Abra `index.html` no navegador.
2. Os dados ficarão salvos no navegador via `localStorage`.
3. O dashboard funciona sem backend.

## Como migrar para Supabase

1. Crie um projeto no Supabase.
2. Vá em SQL Editor e execute:

```sql
create table if not exists public.transactions (
  id uuid primary key,
  user_id uuid default auth.uid(),
  type text not null check (type in ('income', 'expense')),
  description text not null,
  category text not null,
  value numeric not null check (value > 0),
  date date not null,
  created_at timestamptz default now()
);

alter table public.transactions enable row level security;

create policy "Users can view own records"
on public.transactions
for select
using (auth.uid() = user_id);

create policy "Users can insert own records"
on public.transactions
for insert
with check (auth.uid() = user_id);

create policy "Users can update own records"
on public.transactions
for update
using (auth.uid() = user_id);

create policy "Users can delete own records"
on public.transactions
for delete
using (auth.uid() = user_id);
```

3. Renomeie `supabase-config.example.js` para `supabase-config.js`.
4. Preencha a URL e a anon key do seu projeto.
5. Carregue esse arquivo antes do script principal, ou use o valor direto em `window.SUPABASE_CONFIG`.

## Observação importante

O projeto foi preparado para funcionar em localStorage por padrão e tentar usar Supabase quando a configuração estiver presente. Isso permite evoluir sem quebrar a experiência local.
