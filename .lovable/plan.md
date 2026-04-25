## Eksik Trigger'ları Tamamlama

Tek bir migration ile şu trigger'ları ekleyeceğim:

### 1. Auth trigger — yeni kullanıcı kaydı
`auth.users` tablosuna `AFTER INSERT` trigger ekle → `public.handle_new_user()` fonksiyonunu çağırır.
- Yeni kayıt olunca otomatik `profiles` satırı oluşur
- `user_roles` tablosuna varsayılan `'technician'` rolü atanır

```sql
CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
```

### 2. `updated_at` trigger'ları (14 tablo)
Her tabloya `BEFORE UPDATE` trigger ekle → `public.update_updated_at_column()` çağırır:

- profiles, user_roles, machines, technicians, work_orders, work_order_parts
- master_profiles, correction_rules, corrections, learning_cases
- diagnosis_sessions, machine_service_history, machine_logs, repair_videos

Her biri için:
```sql
CREATE TRIGGER set_updated_at_<table>
  BEFORE UPDATE ON public.<table>
  FOR EACH ROW EXECUTE FUNCTION public.update_updated_at_column();
```

### Sonuç
Migration uygulandıktan sonra backend tamamen tamam olur:
- ✅ 14 tablo + RLS
- ✅ 3 storage bucket
- ✅ Seed data
- ✅ Güvenli `user_roles` modeli
- ✅ Auto profile creation trigger (bu migration ile)
- ✅ Auto `updated_at` trigger'ları (bu migration ile)

Onaylarsan tek migration olarak çalıştırırım.