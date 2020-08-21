# Devise
#### Các chức năng cần thiết có thể sử dụng trong devise.

1 . Tạo một User model <br>
```rails g devise user```
2 . Sửa đường dẫn: <br/>
```
# VD: http://localhost:3000/users/sign_in
devise_for :users, path: 'users'
--------------------------------
# VD: http://localhost:3000/u/sign_in
devise_for :users, path: 'u'
```
3 . Tuỳ chỉnh flash message.<br>
- Devise sử dụng file `devise.en.yml` để xác định thông báo flash nào sẽ hiển thị dựa trên hành động nào của người dùng thực hiện. Chúng ta có thể customize chúng theo đa ngôn ngữ.<br>
```
# Trong file: config/locales/devise.en.yml (devise.vi.yml, devise.ja.yml, ...)
en:
     devise:
       confirmations:
         admin_user:
           confirmed: "Your ADMIN email address has been successfully confirmed."
         confirmed: "Your email address has been successfully confirmed."
# Note: Nếu là admin user sẽ nhận được thông báo tuỳ chỉnh giành cho admin. Còn lại tất cả các kiểu user khác sẽ nhận được thông báo mặc định.

```
4 . Chuyển hướng với locale sau khi authentication không thành công.
```
  def set_locale
    I18n.locale = params[:locale]
  end

  def self.default_url_options(options={})
    options.merge({ :locale => I18n.locale })
  end
```
5 . Thêm chức năng xác thực người dùng (confirmable for User).
-  Đầu tiên trong model `models/user.rb` thêm dòng `devise :registerable. :confirmable`.<br>
```devise :registerable, :confirmable```
- Sau đó tạo migration mới như sau: <br>
```rails g migration add_confirmable_to_devise```
- Điều này sẽ tạo ra file migrate như sau: db/migrate/YYYYMMDDxxx_add_confirmable_to_devise.rb: <br>
```
class AddConfirmableToDevise < ActiveRecord::Migration
     # Note: You can't use change, as User.update_all will fail in the down migration
     def up
       add_column :users, :confirmation_token, :string
       add_column :users, :confirmed_at, :datetime
       add_column :users, :confirmation_sent_at, :datetime
       # add_column :users, :unconfirmed_email, :string # Only if using reconfirmable
       add_index :users, :confirmation_token, unique: true
       # User.reset_column_information # Need for some types of updates, but not for update_all.
       # To avoid a short time window between running the migration and updating all existing
       # users as confirmed, do the following
       User.update_all confirmed_at: DateTime.now
       # All existing user accounts should be able to log in after this.
     end
   
     def down
       remove_index :users, :confirmation_token
       remove_columns :users, :confirmation_token, :confirmed_at, :confirmation_sent_at
       # remove_columns :users, :unconfirmed_email # Only if using reconfirmable
     end
   end
```
6 . Chuyển hướng người dùng sau khi click vào link confirm.<br>
```.env
# Tạo một file confirmations_controller.rb trong app/controllers 
    class ConfirmationsController < Devise::ConfirmationsController
      private
      def after_confirmation_path_for(resource_name, resource)
        sign_in(resource) # In case you want to sign in the user
        your_new_after_confirmation_path
      end
    end
# Trong file config/routes.rb thêm vào routes: 
    devise_for :users, controllers: { confirmations: 'confirmations' }
   
    -> Như vậy Devise sẽ sử dụng tuỳ chỉnh sau khi confirm của bạn.
```
7 . Nếu bạn muốn gia hạn thêm thời gian người dùng có thể đăng nhập khi chưa confirm:<br>
```.env

    # config/initializers/devise.rb
    config.allow_unconfirmed_access_for = 365.days

```
- Ngoài ra bạn cũng có thể bỏ qua xác nhận tất cả: <br>
```.env
# in User.rb
    protected
    def confirmation_required?
      false
    end
``` 
8 . Theo dõi ứng dụng<br>
-  Thêm `:trackable` vào model `User`
```.env
    # models/user.rb
    devise :registerable, :trackable
```
- Sau đó tạo migration như sau:<br>
```.env
    rails g migration add_trackable_to_devise
``` 
- Sẽ tạo file như sau: `db/migrate/YYYYMMDDxxx_add_trackable_to_devise.rb`:
```.env
    class AddTrackableToDevise < ActiveRecord::Migration
      def up
        add_column :users, :sign_in_count, :integer, default: 0, null: false
        add_column :users, :current_sign_in_at, :datetime
        add_column :users, :last_sign_in_at, :datetime
        add_column :users, :current_sign_in_ip, :inet
        add_column :users, :last_sign_in_ip, :inet
      end
    
      def down
        remove_columns :users, :sign_in_count, :current_sign_in_at, :last_sign_in_at, :current_sign_in_ip, :last_sign_in_ip
      end
    end
```
--------------
## To be continue...
